# Laudanum Web Shells

## What is Laudanum

Pre-built collection of **ready-to-use web shell files** for multiple languages. Installed by default on Parrot OS and Kali. Used to gain command execution through a web application.

**Location:** `/usr/share/laudanum/`

**Supported languages:** ASP, ASPX, JSP, PHP, and more.

---

## Methodology

```
Copy shell file → Edit (add your IP) → Clean up signatures → Upload via web app → Navigate to shell → Execute commands
```

---

## Step 1: Copy the Shell

```bash
cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
# Copy first — never modify the original
```

---

## Step 2: Modify the Shell

- Open the copied file in a text editor
- Add your **attacker IP** to the `allowedIps` variable (line 59 for ASPX shell)
- **Remove ASCII art and comments** — these are commonly signatured by AV/IDS

```
allowedIps = {"10.10.14.12"}  ← your IP goes here
```

> Without your IP in the allowlist, the shell will block your access even if uploaded successfully
> 

---

## Step 3: Upload via Web App

- Find a **file upload function** in the target web application
- Upload your modified shell file
- Note the **path where it was saved** (shown in upload success message)

---

## Step 4: Navigate to Shell

```
http://status.inlanefreight.local//files/demo.aspx
```

> Note: Windows paths use `\` but browser converts to `/` — use `\\files\` in path if needed, browser will normalize it
> 

---

## Step 5: Execute Commands

- Web shell provides a **browser-based command input** (`cmd /c`)
- Type commands like `systeminfo`, `whoami`, `dir` directly in the browser
- Output appears in the browser window under STDOUT

---

## Key Files by Language

| Language | Path |
| --- | --- |
| ASPX | `/usr/share/laudanum/aspx/shell.aspx` |
| PHP | `/usr/share/laudanum/php/` |
| JSP | `/usr/share/laudanum/jsp/` |
| ASP | `/usr/share/laudanum/asp/` |

---

## Operational Security Notes

- **Remove comments and ASCII art** before uploading — AV/IDS signatures often target these
- **IP allowlist** = only your IP can use the shell — reduces risk of others hijacking it
- Uploaded files may be **renamed or stored randomly** — check upload response carefully
- Some apps may not have a public files directory — need to enumerate where uploads go
- **Clean up after yourself** — delete the shell file when done with the engagement

---

## Assessment

![image.png](Laudanum%20Web%20Shells/image.png)

![image.png](Laudanum%20Web%20Shells/image%201.png)