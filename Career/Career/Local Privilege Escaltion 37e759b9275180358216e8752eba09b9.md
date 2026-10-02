# Local Privilege Escaltion

Status: Not started

```bash
# Check for exposed inspector ports
ss -tunlp

# check sudo -
sudo -l

uname -r

ls -la /opt

getcap -r / 2>/dev/null

sudo /bin/nice /notes/../home/webadmin/root.sh

find /home -name "key" 2>/dev/null

```

[Privilege Esc via Insecure debug interface [ Exposed Debugger ]](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Insecure%20debug%20interface%20%5B%20Expos%2038c759b92751804bbe13da5d3b645103.md)

[Privilege Esc using Facter](Local%20Privilege%20Escaltion/Privilege%20Esc%20using%20Facter%2038c759b92751803e9afbf61daee57b56.md)

[**Privilege Esc via cap_setuid Capability on Python Interpreter**](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20cap_setuid%20Capability%20on%20Python%20%2038c759b927518057bc0fe36f8f7c27d6.md)

[**Privilege Esc Passwordless Sudo Misconfiguration**](Local%20Privilege%20Escaltion/Privilege%20Esc%20Passwordless%20Sudo%20Misconfiguration%2038c759b9275180e4b390eb19b11f1e33.md)

[Privilege Esc via **Sudo Path Traversal via Incomplete Canonicalisation**](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Sudo%20Path%20Traversal%20via%20Incomple%2038c759b927518018a13ed9644c53c402.md)

[**Privilege Esc via Sudo Misconfiguration and Writable Script Abuse**](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Sudo%20Misconfiguration%20and%20Writab%2038c759b927518047b08ee8b982037875.md)

[Privilege Esc via Sudo Misconfiguration (Vim Abuse)](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Sudo%20Misconfiguration%20(Vim%20Abuse%2038c759b927518043a716fa9ec665cb10.md)

[Privilege Esc via SUID Python Binary](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20SUID%20Python%20Binary%2038c759b92751801fa459c1152c37469b.md)

[Privilege Esc via Docker Group Membership](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Docker%20Group%20Membership%2038c759b9275180ffa1f3ef0248b5385b.md)

[Privilege Esc via Exposed Backup SSH Key](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Exposed%20Backup%20SSH%20Key%2038c759b9275180de82eef69333688e04.md)

[Privilege Esc via Wildcard Injection (Tar Checkpoint Abuse)](Local%20Privilege%20Escaltion/Privilege%20Esc%20via%20Wildcard%20Injection%20(Tar%20Checkpoi%2038c759b9275180cca89fd0278c978593.md)