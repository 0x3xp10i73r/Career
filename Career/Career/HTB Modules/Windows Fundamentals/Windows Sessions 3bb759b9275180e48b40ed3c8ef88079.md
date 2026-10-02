# Windows Sessions

## Session Types

### Interactive (Local Logon)

User physically or remotely authenticates with credentials.

**Three ways to initiate:**

- Direct login at the machine
- `runas` command (secondary logon session)
- Remote Desktop (RDP) connection

### Non-Interactive

No credentials required — used by OS to run services and scheduled tasks automatically. **No password associated.**

---

## Non-Interactive Account Types

| Account | Also Known As | Privilege Level | Purpose |
| --- | --- | --- | --- |
| **Local System** | `NT AUTHORITY\SYSTEM` | **Highest** (above local admins) | Core OS tasks, Windows services |
| **Local Service** | `NT AUTHORITY\LocalService` | Low (similar to local user) | Limited services, minimal network access |
| **Network Service** | `NT AUTHORITY\NetworkService` | Low (similar to domain user) | Services that need authenticated network sessions |

---

## Privilege Hierarchy

```
NT AUTHORITY\SYSTEM (LocalSystem)
    ↓ (more powerful than)
BUILTIN\Administrators
    ↓
NT AUTHORITY\NetworkService
    ≈
NT AUTHORITY\LocalService
    ↓
Standard User
```

---

## Pentesting Relevance

| Account | Why it Matters |
| --- | --- |
| `SYSTEM` | Ultimate goal on a local Windows machine — full control |
| `LocalService` | If you compromise a service running as this, limited damage |
| `NetworkService` | Can authenticate to network resources — useful for lateral movement |

**Common paths to SYSTEM:**

- Service binary replacement (service runs as SYSTEM)
- Token impersonation (SeImpersonatePrivilege)
- Kernel exploits
- PrintSpoofer / RoguePotato (when you have `SeImpersonatePrivilege`)

---

## runas — Secondary Logon (Interactive)

```bash
# Run command as another user
runas /user:domain\username cmd.exe

# Run as local admin
runas /user:administrator cmd.exe

# Run with saved credentials
runas /savecred /user:administrator cmd.exe
```

---

## Key Takeaways

- `NT AUTHORITY\SYSTEM` = **most powerful account on Windows** — more powerful than local admin
- Non-interactive accounts have **no password** → can't directly log in as them, but can impersonate
- Services starting at boot typically run as one of these three non-interactive accounts
- Compromising a service running as SYSTEM = instant privilege escalation
- `NetworkService` can **authenticate to domain resources** using the computer account → useful for pivoting in AD environments