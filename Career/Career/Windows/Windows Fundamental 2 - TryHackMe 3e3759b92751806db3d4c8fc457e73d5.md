# Windows Fundamental 2 - TryHackMe

## MSConfig (System Configuration)

```
Win + R → msconfig
```

Requires **local admin rights**. Used for advanced troubleshooting and startup diagnostics.

**5 Tabs:**

| Tab | Purpose |
| --- | --- |
| General | Boot mode: Normal / Diagnostic / Selective |
| Boot | OS boot options |
| Services | All services regardless of state |
| Startup | Redirects to Task Manager (use `shell:startup` on servers) |
| Tools | Launch various system utilities |

---

## Quick Reference — All Tool Commands

| Tool | Command/File |
| --- | --- |
| System Configuration | `msconfig` |
| Computer Management | `compmgmt.msc` |
| Local Users & Groups | `lusrmgr.msc` |
| System Information | `msinfo32.exe` |
| Resource Monitor | `resmon.exe` |
| Registry Editor | `regedit.exe` or `regedt32.exe` |
| Control Panel | `control.exe` |
| UAC Settings | `UserAccountControlSettings.exe` |
| Task Manager | `taskmgr` / `Ctrl+Shift+Esc` |
| Windows Troubleshooting | `C:\Windows\System32\control.exe /name Microsoft.Troubleshooting` |
| IP Config (full path) | `C:\Windows\System32\cmd.exe /k %windir%\system32\ipconfig.exe` |
| Startup folder (servers) | `shell:startup` |

---

## UAC Security Levels (Slider)

| Level | Description |
| --- | --- |
| Always notify | Notifies for ALL changes (apps + user) — most secure |
| Notify for apps (default) | Only notifies when apps make changes |
| Notify without dimming | Same as above but no secure desktop dim |
| Never notify | Disabled — no warnings at all |

---

## Computer Management (compmgmt.msc)

**Three sections:**

### System Tools

| Tool | Purpose |
| --- | --- |
| Task Scheduler | Create/manage automated tasks on schedule or at events |
| Event Viewer | View system logs — audit trail for troubleshooting/investigation |
| Shared Folders | List all shares, active sessions, open files |
| Local Users & Groups | Same as `lusrmgr.msc` |
| Performance Monitor (perfmon) | Real-time or logged performance data |
| Device Manager | View/configure/disable hardware |

### Storage

- **Disk Management** — create/extend/shrink partitions, assign drive letters

### Services & Applications

- **Services** — view/start/stop/configure all services (startup type: Automatic/Manual/Disabled)
- **WMI Control** — configure WMI service

---

## Event Viewer — 5 Event Types

- **Error** — significant problem (data loss, failure)
- **Warning** — not immediately serious but could be
- **Information** — successful operation
- **Success Audit** — successful audited security event
- **Failure Audit** — failed audited security event

**Standard Windows Logs:** Application, Security, Setup, System, Forwarded Events

---

## System Information (msinfo32.exe)

Three sections:

- **Hardware Resources** — IRQ, DMA, memory addresses
- **Components** — installed hardware (Display, Input, Network, etc.)
- **Software Environment** — running services, startup programs, environment variables, network connections

**Search bar at bottom** — search across all sections (e.g., search "IP address" under Components)

---

## Resource Monitor (resmon.exe)

Real-time monitoring across 4 tabs:

- **CPU** — per-process CPU usage
- **Memory** — RAM usage per process
- **Disk** — disk I/O per process
- **Network** — network usage per process

Also shows: file handles, modules, deadlocked processes, file locking conflicts

---

## CMD — Key Commands

```bash
hostname          # computer name
whoami            # current logged-in user
ipconfig          # network settings
ipconfig /all     # detailed network info (all adapters)
ipconfig /?       # help for ipconfig
netstat           # protocol stats + TCP/IP connections
netstat -a        # all connections and listening ports
net               # manage network resources
net help user     # help for net sub-commands (NOT net user /?)
cls               # clear screen
```

> Note: `net` uses `net help <subcommand>` not `net <subcommand> /?`
> 

---

## Environment Variables

| Variable | Value |
| --- | --- |
| `%windir%` | `C:\Windows` |
| `%SystemRoot%` | `C:\Windows` |
| `ComSpec` | `%SystemRoot%\system32\cmd.exe` |
| `%TEMP%` | Temp folder path |

**View via:** Control Panel → System → Advanced System Settings → Environment Variables

OR: `msinfo32` → Software Environment → Environment Variables

---

## Pentesting Relevance

| Tool | Red Team Use |
| --- | --- |
| Task Scheduler | Persistence — add malicious scheduled task |
| Event Viewer | Check what's been logged during engagement |
| Shared Folders | Enumerate accessible shares |
| Services | Find misconfigured services for privesc |
| msinfo32 | Quick system fingerprint |
| Environment Variables | Find paths for DLL hijacking |
| netstat | See active connections, find C2 beacons |
| net commands | Enumerate users, groups, shares |