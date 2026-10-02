# Windows Services & Processes

## Windows Services Overview

- Long-running processes managed by **Service Control Manager (SCM)**
- Can start automatically at boot, run without user logged in
- Managed via: `services.msc`, `sc.exe`, or PowerShell `Get-Service`
- Three categories: **Local Services**, **Network Services**, **System Services**
- Misconfigurations = **common privilege escalation vector**

---

## Service Startup Types

- **Automatic** — starts at boot
- **Automatic (Delayed)** — starts after boot completes
- **Manual** — only starts when explicitly called
- **Disabled** — cannot be started

---

## Critical System Services (Cannot Stop Without Restart)

| Process | Function |
| --- | --- |
| `smss.exe` | Session Manager — handles system sessions |
| `csrss.exe` | Client Server Runtime — user-mode Windows subsystem |
| `wininit.exe` | Initializes Windows on restart after installs |
| `logonui.exe` | Facilitates user login UI |
| `lsass.exe` | **Local Security Auth** — verifies logons, generates access tokens, stores credentials in memory |
| `services.exe` | Manages start/stop of all services |
| `winlogon.exe` | Handles secure attention sequence (Ctrl+Alt+Del), loads user profile |
| `System` | Runs the Windows kernel |
| `svchost.exe` | Hosts multiple DLL-based services (runs many instances) |

---

## LSASS — High Value Target

`lsass.exe` = **Local Security Authority Subsystem Service**

- Enforces security policy
- Verifies logon attempts → creates access tokens
- Handles password changes
- **Stores credentials in memory** → cleartext + hashes extractable with tools like Mimikatz
- All logon/logoff events logged in **Windows Security Log**

> LSASS = primary target for credential dumping during post-exploitation
> 

---

## Key Commands

```powershell
# List all running services
Get-Service | ? {$_.Status -eq "Running"}

# Filter and format specific service
Get-Service | ? {$_.Status -eq "Running"} | select -First 2 | fl

# Start/stop a service
sc.exe start <servicename>
sc.exe stop <servicename>

# Query a specific service
sc.exe query <servicename>

Get-WmiObject -Class Win32_Service | Where-Object { $_.PathName -match "Update" } | Select-Object -Property Name, PathName
```

---

## Task Manager — Tabs Reference

| Tab | What it Shows |
| --- | --- |
| **Processes** | Running apps + background processes + resource usage |
| **Performance** | CPU, RAM, disk, network, GPU graphs + uptime |
| **App History** | Per-app resource usage over time |
| **Startup** | Apps that launch at boot + startup impact |
| **Users** | Logged-in users + their process/resource usage |
| **Details** | PID, status, username, CPU/memory per process |
| **Services** | Name, PID, description, status of all services |

**Open Task Manager:**

- `Ctrl + Shift + Esc`
- `Ctrl + Alt + Del` → Task Manager
- Right-click taskbar → Task Manager
- Type `taskmgr` in CMD/PowerShell

---

## Sysinternals Tools

Access without downloading:

```bash
\\live.sysinternals.com\tools\procdump.exe -accepteula
```

| Tool | Purpose |
| --- | --- |
| **Process Explorer** | Enhanced Task Manager — shows DLLs, handles, parent-child relationships |
| **Process Monitor** | Monitor file system, registry, network activity per process |
| **TCPView** | Monitor active network connections per process |
| **PSExec** | Remote management/execution via SMB |
| **ProcDump** | Dump process memory → used to dump LSASS |

---

## Pentesting Relevance

| Target | Why |
| --- | --- |
| `lsass.exe` | Dump with ProcDump/Mimikatz → extract creds from memory |
| Service misconfigurations | Weak permissions on service binary → replace with payload → SYSTEM |
| `svchost.exe` | Many legit processes → malware hides here |
| Startup services | Persistence mechanism — malware adds itself here |
| Sysinternals Process Explorer | Find unusual parent-child process relationships (injection detection) |

---

## Finding Non-Standard Services (For Q1)

```powershell
# List all services with details
Get-Service | fl

# Look for non-Microsoft update services
Get-WmiObject win32_service | select Name, DisplayName, PathName, StartMode | where {$_.StartMode -eq "Auto"}
```

Connect via RDP then check `services.msc` or run:

```powershell
Get-Service | ? {$_.DisplayName -like "*update*"} | select Name, DisplayName, Status
```

Look for non-standard ones (not Windows Update, Adobe, etc.) — the answer is the **service Name** (executable name), not the DisplayName.