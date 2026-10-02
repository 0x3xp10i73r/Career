# Window Fundamental 1 - TryHackMe

## Windows Desktop Components

- **Desktop** — shortcuts, icons, right-click for context menu (display settings, personalize)
- **Start Menu** — apps, files, utilities, account actions, power options
- **Taskbar** — open apps appear here; hover for thumbnail preview
- **Notification Area** — bottom right: date/time, volume, network icons
- **Search Box (Cortana)** — search apps, files, settings
- **Task View** — virtual desktop management

---

## NTFS File System (Key Points)

**Advantages over FAT32:**

- Files larger than 4GB ✅
- Granular permissions ✅
- Folder/file compression ✅
- Encryption (EFS) ✅
- **Journaling** — auto-repairs on failure ✅

**NTFS Permissions:** Full Control, Modify, Read & Execute, List Folder Contents, Read, Write

### Alternate Data Streams (ADS) — Security Relevant

- Every NTFS file has at least one stream (`$DATA`)
- ADS = hidden additional data streams attached to a file
- **Not visible in Windows Explorer by default**
- Used by: malware (hiding data), Windows (marking downloaded files from internet)
- View with PowerShell or 3rd party tools

```powershell
# View ADS on a file
Get-Item -Path file.txt -Stream *

# Read specific ADS
Get-Content -Path file.txt -Stream hiddenstream
```

---

## System32 & Environment Variables

- `C:\Windows\System32` = critical OS files + most admin tools
- **Never delete from System32** — will break Windows
- Access Windows folder via environment variable: `%windir%`

```bash
echo %windir%        # C:\Windows
echo %SystemRoot%    # C:\Windows
echo %TEMP%          # temp folder path
```

---

## User Accounts

| Type | Capabilities |
| --- | --- |
| **Administrator** | Add/delete users, modify groups, install software, system changes |
| **Standard User** | Only modify own files/folders |

**User profile path:** `C:\Users\<username>`

**Manage users:** `Win + R` → `lusrmgr.msc`

---

## User Account Control (UAC)

- Introduced in Windows Vista
- Admins log in with **standard privilege by default** — elevated only when needed
- UAC prompt appears for actions requiring elevated rights
- **Shield icon** on program = UAC elevation required to run
- UAC does NOT apply to the built-in Administrator account by default
- Reduces malware impact — malware runs in context of current user's privileges

---

## Settings vs Control Panel

| Settings | Control Panel |
| --- | --- |
| Modern (since Win 8) | Legacy but still used |
| Primary location now | Complex/advanced settings |
| Touch-friendly | More granular options |

Both accessible from Start Menu. Some Settings pages redirect to Control Panel.

---

## Task Manager

**Open via:**

- Right-click taskbar → Task Manager
- **`Ctrl + Shift + Esc`** ← keyboard shortcut
- `Ctrl + Alt + Del` → Task Manager
- Type `taskmgr` in Run/CMD

**Tabs:** Processes, Performance, App History, Startup, Users, Details, Services

**Default view** = Simple. Click **More details** for full view.

---

## Key Takeaways

- ADS = **stealth data hiding technique** — check files with PowerShell during forensics/hunting
- UAC = security feature — malware running as standard user can't make system changes
- `%windir%` and `%SystemRoot%` = environment variables pointing to Windows directory
- Task Manager = first stop for seeing what's running on a compromised system
- `lusrmgr.msc` = fastest way to enumerate local users and group memberships