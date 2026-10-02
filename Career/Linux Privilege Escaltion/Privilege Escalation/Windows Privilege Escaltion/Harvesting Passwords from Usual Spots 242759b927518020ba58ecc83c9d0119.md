# Harvesting Passwords from Usual Spots

## **🔐 Credential Discovery on Windows – Summary Notes**

---

### **📦 1.Unattended Windows Installations**

Used in large-scale deployments; credentials may be hardcoded in config files.

**📁 Common file locations:**

```
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```

**🔍 Look for:**

```
<Credentials>
    <Username>Administrator</Username>
    <Domain>thm.local</Domain>
    <Password>MyPassword123</Password>
</Credentials>
```

---

### **🧠 2.PowerShell History**

Stored in plain text — can contain passwords used in commands.

**📁 Location (from cmd):**

```powershell
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

**📁 From PowerShell:**

```powershell
Get-Content "$Env:userprofile\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt"
```

---

### **💾 3.Saved Windows Credentials**

**📌 List saved credentials:**

```powershell
cmdkey /list
```

**🔐 Use a saved credential:**

```powershell
runas /user:<domain\user> cmd.exe
```

**💡 Tip:** You won’t see passwords, but if you see stored creds, you can *attempt* to reuse them.

---

### **🌐 4.IIS Web Configuration Files**

IIS may store sensitive credentials for DB access in web.config.

**📁 Common paths:**

```
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

**🔍 To extract connection strings:**

```
type "C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config" | findstr connectionString
```

**🧠 Look for:** User ID=...;Password=...

---

### **🔑 5.PuTTY Saved Sessions**

PuTTY may store **proxy** credentials in registry (not SSH passwords).

**📍 Registry path:**

```
reg query "HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions" /f "Proxy" /s
```

**👀 Look for values like:**

- ProxyUsername
- ProxyPassword

---

### **🔎 General Tip:**

Any software that **stores credentials** (browsers, FTP clients, SSH tools, email clients, etc.) is worth investigating for plaintext secrets.

---

## **🧰 Handy One-Liners Recap:**

| **Purpose** | **Command** |
| --- | --- |
| PowerShell history | type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt |
| Saved credentials | cmdkey /list |
| Use saved cred | runas /user:<user> cmd.exe |
| IIS DB creds | `type <web.config path> |
| PuTTY creds | reg query "HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions" /f "Proxy" /s |

---

Let me know if you’d like this exported as a PDF cheat sheet or Markdown doc!