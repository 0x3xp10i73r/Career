# Desktop Experience vs. Server Core

Two Windows Server installation options: **Server Core** (minimal, CLI-based) vs. **Desktop Experience** (full GUI).

---

## Overview

| Aspect | Server Core | Desktop Experience |
| --- | --- | --- |
| **Released** | Windows Server 2008 | Original server GUI |
| **Interface** | Command-line / PowerShell / remote | Full GUI |
| **Footprint** | Smaller disk & memory usage | Larger |
| **Attack Surface** | Smaller | Larger |
| **Management** | CLI, PowerShell, MMC, RSAT (remote) | Local GUI + CLI |
| **Learning Curve** | Steeper | Easier for casual users |

---

## Server Core — Key Facts

- Minimalistic environment — only **key Server functionality**.
- All config/maintenance via **CLI, PowerShell, or remote management** (MMC / RSAT).
- Supports **some graphical programs**:
    - Registry Editor, Notepad, System Information, Windows Installer, Task Manager, PowerShell
- Supports **some Sysinternals tools**:
    - Active Directory Explorer, Process Explorer, Process Monitor, TCPView
- **As of Windows Server 2019**: choice between Core and Desktop Experience must be made at **installation** — **cannot be rolled back**.

---

## Sconfig (Server Core Initial Setup)

- **Text-based interface** (actually a **VBScript executed by WScript**).
- Used for common admin tasks:
    - Configure networking
    - Check/install Windows updates
    - Account management
    - Configure remote management
    - Activate Windows
    - And more

```
# Launch Sconfig on Server Core
sconfig
```

---

## Applications: Server Core vs. Desktop Experience

| Application | Server Core | Desktop Experience |
| --- | --- | --- |
| Command Prompt | ✅ Available | ✅ Available |
| Windows PowerShell / .NET | ✅ Available | ✅ Available |
| Regedit | ✅ Available | ✅ Available |
| Taskmgr | ✅ Available | ✅ Available |
| Remote Desktop Services | ✅ Available | ✅ Available |
| Diskmgmt.msc | ❌ Not Available | ✅ Available |
| Server Manager | ❌ Not Available | ✅ Available |
| Mmc.exe | ❌ Not Available | ✅ Available |
| Eventvwr | ❌ Not Available | ✅ Available |
| Services.msc | ❌ Not Available | ✅ Available |
| Control Panel | ❌ Not Available | ✅ Available |
| Windows Explorer | ❌ Not Available | ✅ Available |
| Internet Explorer / Edge | ❌ Not Available | ✅ Available |

> Many `.msc` consoles are **not available locally** on Server Core — must be used **remotely** via MMC/RSAT.
> 

---

## Unsupported Server Applications on Server Core

- Microsoft Server Virtual Machine Manager 2019 (**SCVMM**)
- System Center Data Protection Manager 2019
- SharePoint Server 2019
- Project Server 2019

---

## Choosing Between the Two

Decision should be based on:

1. **Business need** — what the server is used for
2. **Intended use** — role and workload
3. **Administrator skill level** — comfort with CLI/PowerShell

> Server Core = lighter, less resource-intensive, **steeper learning curve**, harder to manage.
Desktop Experience = full GUI, easier for casual admins, **larger attack surface**.
> 

---

## Pentesting Uses

| Use Case | Technique |
| --- | --- |
| Smaller attack surface | Server Core = fewer GUI tools to exploit |
| Remote management | MMC/RSAT from attacker-controlled host |
| Enumeration | Use PowerShell/WMIC (GUI tools unavailable) |
| Lateral movement | RDP still available on both |
| Tool availability | Sysinternals may be present on Server Core |

---

## Key Takeaways

- **Server Core** = minimal, CLI-based, smaller footprint, smaller attack surface.
- **Desktop Experience** = full GUI, easier to manage, larger attack surface.
- **Cannot convert** between Core and Desktop Experience after installation (Server 2019+).
- **Sconfig** = text-based setup tool on Server Core (VBScript/WScript).
- Many `.msc` consoles, Server Manager, and browsers are **missing on Server Core** — use **remote management** instead.
- Choice depends on **business need, server role, and admin skill level**.