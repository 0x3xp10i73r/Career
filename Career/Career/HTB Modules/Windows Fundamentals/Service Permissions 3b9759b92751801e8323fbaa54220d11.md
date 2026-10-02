# Service Permissions

## Why Service Permissions Matter

Services = **common privilege escalation and persistence vector** because:

- Many run as **LocalSystem** (highest Windows privilege)
- 3rd party software often misconfigures service permissions
- Weak permissions on the **service binary path** → replace exe with malicious payload
- **Recovery tab** can execute programs on failure → another attack vector

---

## Built-in Service Accounts (Least → Most Privilege)

| Account | Privilege Level | Notes |
| --- | --- | --- |
| `LocalService` | Low | Minimal local privileges, anonymous network access |
| `NetworkService` | Low-Medium | Limited local, network access with computer credentials |
| `LocalSystem` | **Highest** | Full local admin, dangerous if service is compromised |

> Best practice: create **dedicated service accounts** with **least privilege** needed
> 

---

## sc.exe — Service Management Commands

```bash
# Query service configuration
sc qc wuauserv

# Query service on remote host
sc \\<hostname or IP> query <ServiceName>

# Start / stop service (requires admin)
sc start wuauserv
sc stop wuauserv

# Change service binary path (dangerous if misconfigured)
sc config wuauserv binPath=C:\malicious\payload.exe

# View security descriptor (DACL) of a service
sc sdshow wuauserv
```

---

## Reading SDDL Output (Security Descriptor Definition Language)

```
D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
```

**Format breakdown:**

```
D:           → DACL follows
(A;;...;;;AU) → Allow (A) these permissions to Authenticated Users (AU)
(A;;...;;;BA) → Allow to Built-in Administrators (BA)
(A;;...;;;SY) → Allow to System (SY)
```

**Common permission codes:**

| Code | Permission |
| --- | --- |
| `CC` | Query service config (SCM) |
| `LC` | Query service status |
| `SW` | Enumerate dependent services |
| `RP` | Start service |
| `WP` | Stop service |
| `DT` | Pause/continue |
| `LO` | Interrogate service |
| `RC` | Read security descriptor |
| `WD` | Write DACL |
| `WO` | Write owner |

**Common principal codes:**

| Code | Meaning |
| --- | --- |
| `AU` | Authenticated Users |
| `BA` | Built-in Administrators |
| `SY` | System |
| `BU` | Built-in Users |
| `WD` | Everyone |

---

## PowerShell — View Service ACL from Registry

```powershell
# View service permissions in readable format
Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List

# Check all running services
Get-Service | ? {$_.Status -eq "Running"} | fl

# Find services with weak permissions (for privesc)
Get-WmiObject win32_service | select Name, DisplayName, PathName, StartMode
```

---

## Privilege Escalation via Service Misconfigurations

| Misconfiguration | Attack |
| --- | --- |
| Weak NTFS permissions on service binary directory | Replace `.exe` with malicious payload |
| Service runs as LocalSystem + writable binary path | Execute payload as SYSTEM |
| Recovery tab configured to run program on failure | Set malicious program as failure action |
| User has `RP` (start) + `WP` (stop) + modify on binary | Replace binary, restart service → SYSTEM |
| Unquoted service path with spaces | Inject malicious binary in the path gap |

**Check binary path permissions:**

```bash
icacls "C:\path\to\service\binary.exe"
# Look for: Users (M) or Users (F) → writable by non-admins = vulnerable
```

---

## Key Takeaways

- `sc qc <service>` = fastest way to see service config including binary path and run-as account
- `sc sdshow <service>` = view raw DACL — decode with SDDL knowledge
- `Get-ACL` on registry path = cleaner, more readable output for service ACL
- Services running as **LocalSystem with writable binary paths** = immediate privesc opportunity
- **Recovery tab** → run program on failure = persistence + execution vector
- Always check service binary directory permissions when looking for privesc paths
- Script-based enumeration (`sc`, `Get-ACL`) scales better than GUI for large environments