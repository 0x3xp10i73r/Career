# Introduction to Windows

# Windows Fundamentals — Introduction Notes

## Windows Version History (Key for Pentesting)

| OS | Version Number |
| --- | --- |
| Windows NT 4 | 4.0 |
| Windows 2000 | 5.0 |
| Windows XP | 5.1 |
| Windows Server 2003/2003 R2 | 5.2 |
| Windows Vista, Server 2008 | 6.0 |
| Windows 7, Server 2008 R2 | 6.1 |
| Windows 8, Server 2012 | 6.2 |
| Windows 8.1, Server 2012 R2 | 6.3 |
| Windows 10, Server 2016, Server 2019 | 10.0 |

> Legacy systems (XP, Server 2003, 2008) = still found in real environments → often unpatched → prime targets
> 

---

## Check Windows Version (PowerShell)

```powershell
# Get OS version and build number
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber

# Other useful WMI classes
Get-WmiObject -Class Win32_Process      # running processes
Get-WmiObject -Class Win32_Service      # services
Get-WmiObject -Class Win32_Bios         # BIOS info

# Remote system query
Get-WmiObject -Class win32_OperatingSystem -ComputerName <hostname>
```

---

## Remote Access Methods (Know These)

| Method | Protocol/Port | Notes |
| --- | --- | --- |
| RDP | TCP 3389 | Primary Windows remote access |
| SSH | TCP 22 | More common on Linux, available on Windows 10+ |
| WinRM/PS Remoting | TCP 5985/5986 | PowerShell-based remote management |
| VNC | TCP 5900 | GUI-based, older |
| FTP | TCP 21 | File transfer only |
| VPN | Various | Encrypted tunnel |

---

## RDP Tools

### From Windows

```
mstsc.exe → Remote Desktop Connection (built-in)
```

> Check for saved `.rdp` files during engagements — may contain credentials or saved profiles
> 

### From Linux

```bash
# xfreerdp (most common for HTB/pentest)
xfreerdp /v:<targetIP> /u:htb-student /p:Password

# With drive redirection (file transfer)
xfreerdp /v:<IP> /u:user /p:pass /drive:share,/tmp

# Other options
remmina    # GUI-based
rdesktop   # older, basic
```

---

## Pentesting Notes

- **Saved `.rdp` files** = look for these on compromised Windows hosts — may contain server addresses, usernames
- **RDP disabled by default** on Windows — if you find it enabled → potential foothold or pivot point
- **Legacy Windows versions** in scope = check for EternalBlue (MS17-010), PrintNightmare, etc. first
- **WMI** is a powerful enumeration and lateral movement tool — `Get-WmiObject` works locally and remotely

---

## Key Takeaways

- Know Windows version numbers — they tell you what exploits apply and what features exist
- RDP = port 3389, client/server model, GUI access to remote Windows
- `xfreerdp` = standard tool from Linux for RDP connections throughout HTB/real engagements
- WMI (`Get-WmiObject`) = powerful built-in enumeration tool for local and remote Windows hosts
- Always check for saved RDP profiles (`.rdp` files) — common sysadmin habit that leaks info