# Windows Management Instrumentation (WMI)

## What is WMI

A PowerShell subsystem and Windows core component (since Windows 2000) for **local and remote system monitoring, management, and code execution** across corporate networks.

---

## WMI Components

| Component | Purpose |
| --- | --- |
| WMI Service | Runs at boot — intermediary between providers, repository, and apps |
| WMI Providers | Monitor events/data for specific objects |
| Classes | Used by providers to pass data to WMI service |
| Methods | Attached to classes — perform actions (e.g., start/stop processes) |
| WMI Repository | Database of all static WMI data |
| CIM Object Manager | Requests data from providers, returns to applications |
| WMI API | Allows apps to access WMI infrastructure |
| WMI Consumer | Sends queries via CIM Object Manager |

---

## What WMI Can Do

- Get status of local/remote systems
- Configure security settings remotely
- Manage user/group permissions
- **Execute code** on remote machines
- Schedule processes
- Set up logging
- Modify system properties

> WMI = powerful red team tool for **enumeration AND lateral movement**
> 

---

## WMIC (Command-line)

> ⚠️ Note: WMIC is **deprecated** in newer Windows versions but still functional
> 

```bash
# Open interactive WMIC shell
wmic

# Get hostname
wmic computersystem get name

# List OS information (brief)
wmic os list brief

# Get serial number
wmic bios get serialnumber

# Get OS details
wmic os get Caption,Version,BuildNumber,SerialNumber

# List running processes
wmic process list brief

# List services
wmic service list brief

# Get help
wmic /?
```

---

## PowerShell WMI — Get-WmiObject

```powershell
# OS information
Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory,BuildNumber,SerialNumber,Version | ft

# Serial number (from BIOS)
Get-WmiObject -Class Win32_BIOS | select SerialNumber

# Running processes
Get-WmiObject -Class Win32_Process

# Services
Get-WmiObject -Class Win32_Service

# Computer system info
Get-WmiObject -Class Win32_ComputerSystem

# Query remote machine
Get-WmiObject -Class Win32_OperatingSystem -ComputerName <hostname>

# Rename a file via WMI method
Invoke-WmiMethod -Path "CIM_DataFile.Name='C:\users\public\old.csv'" -Name Rename -ArgumentList "C:\Users\Public\new.csv"
# ReturnValue: 0 = success
```

---

## Common WMI Classes

| Class | What it Returns |
| --- | --- |
| `Win32_OperatingSystem` | OS version, build, serial, directories |
| `Win32_Process` | Running processes |
| `Win32_Service` | Installed services |
| `Win32_BIOS` | BIOS info + **serial number** |
| `Win32_ComputerSystem` | Computer name, manufacturer, model |
| `Win32_UserAccount` | Local user accounts |
| `Win32_NetworkAdapterConfiguration` | Network config, IPs |
| `Win32_LogicalDisk` | Disk information |

---

## Answer to Q1 — Find Serial Number

```powershell
# PowerShell
Get-WmiObject -Class Win32_OperatingSystem | select SerialNumber

# Or via BIOS
Get-WmiObject -Class Win32_BIOS | select SerialNumber

# CMD
wmic bios get serialnumber
# or
wmic os get SerialNumber
```

---

## Pentesting Uses of WMI

| Use Case | Command |
| --- | --- |
| Remote code execution | `Invoke-WmiMethod Win32_Process Create` |
| Enumerate local users | `Get-WmiObject Win32_UserAccount` |
| Enumerate processes | `Get-WmiObject Win32_Process` |
| Lateral movement | WMI remote execution (`-ComputerName`) |
| Persistence | WMI event subscriptions (advanced) |
| Gather system info | `Win32_ComputerSystem`, `Win32_BIOS`, `Win32_OS` |

---

## Key Takeaways

- WMI = built-in Windows management tool = **LOLBin for enumeration and lateral movement**
- `Get-WmiObject` = PowerShell's WMI interface — works locally and remotely
- `Invoke-WmiMethod` = execute actions via WMI (rename files, start processes, etc.)
- **ReturnValue: 0** = success for WMI method calls
- WMIC = deprecated but still works — use `Get-WmiObject` for scripting
- WMI can reach **remote machines** without needing additional tools — ideal for lateral movement