# Operating System Structure

## Root Directory

Windows root = `C:\` (boot partition where OS is installed)
Other drives assigned letters: `D:\`, `E:\`, etc.

---

## Key Directories & Their Purpose

| Directory | Purpose | Pentest Relevance |
| --- | --- | --- |
| `C:\PerfLogs` | Windows performance logs | Usually empty |
| `C:\Program Files` | 64-bit programs (on 64-bit OS) | Installed software enumeration |
| `C:\Program Files (x86)` | 32-bit/16-bit programs | Installed software enumeration |
| `C:\ProgramData` | **Hidden** — app data accessible by all users regardless of who's logged in | Config files, sometimes credentials |
| `C:\Users` | All user profiles | Target for credential/data hunting |
| `C:\Users\Default` | Template for new user profiles | Persistence location sometimes |
| `C:\Users\Public` | Shared across all users, shared over network by default | File staging, lateral movement |
| `C:\Users\<user>\AppData` | **Hidden** — per-user app data (Roaming/Local/LocalLow) | Browser data, saved creds, app configs |
| `C:\Windows` | Core OS files |  |
| `C:\Windows\System32` | Core DLLs + Windows API (64-bit) | DLL hijacking target |
| `C:\Windows\SysWOW64` | 32-bit DLLs on 64-bit OS | DLL hijacking target |
| `C:\Windows\WinSxS` | Component store — all Windows components, updates, service packs |  |

---

## AppData Subfolders (Important)

| Subfolder | Synced? | Notes |
| --- | --- | --- |
| `Roaming` | ✅ Yes | Machine-independent (follows user across network) |
| `Local` | ❌ No | Machine-specific, never synced |
| `LocalLow` | ❌ No | Lower integrity level — used by sandboxed apps (browsers in protected mode) |

---

## Directory Exploration Commands

```bash
# List all files including hidden (C drive)
dir c:\ /a

# Graphical directory tree of specific path
tree "c:\Program Files (x86)\VMware"

# Walk ALL files on C drive, one screen at a time
tree c:\ /f | more
```

---

## Pentesting Relevance

| Location | What to Look For |
| --- | --- |
| `C:\Users\<user>\AppData\Roaming` | Browser saved passwords, app configs |
| `C:\Users\<user>\AppData\Local` | App-specific data, sometimes tokens/keys |
| `C:\ProgramData` | App configs, sometimes cleartext credentials |
| `C:\Users\Public` | Files left by admins, staging area for transfers |
| `C:\Windows\System32` | DLL hijacking opportunities |
| `C:\Program Files` | Identify installed software → look for known vulns |

---

## Key Takeaways

- `ProgramData` and `AppData` are **hidden by default** — use `/a` flag or `dir /a` to see them
- `System32` = where Windows looks for DLLs by default → prime target for **DLL hijacking**
- `C:\Users\Public` = writable by all users → useful for **staging payloads**
- `tree c:\ /f | more` = slow but comprehensive way to map entire filesystem
- Always check `AppData\Roaming` on compromised user accounts — browsers store session data and credentials there