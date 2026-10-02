# OS Detection and Traceroute

# **🖥️ Nmap OS Detection & Traceroute**

## **🔍 OS Detection (-O)**

Nmap can identify the operating system running on a target using behavior analysis and response patterns.

### **✅ Usage:**

```
sudo nmap -sS -O TARGET_IP
```

### **📌 Example Output:**

```
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.13
OS details: Linux 3.13
```

### **⚙️ Notes:**

- Nmap needs **at least one open and one closed port** for accurate OS detection.
- OS fingerprints can be **inaccurate** due to:
    - **Virtualization**
    - **Firewalls**
    - **Network filtering**
- OS guesses are sometimes **close but not exact** (e.g., Nmap guessed 3.13, actual was 3.16).
- Still useful for narrowing down attack surface.

---

## **🌐 Traceroute (-traceroute)**

Nmap can trace the path packets take to reach the target host, identifying routers along the path.

### **✅ Usage:**

```
sudo nmap -sS --traceroute TARGET_IP
```

### **📌 Example Output:**

```
TRACEROUTE
HOP RTT     ADDRESS
1   1.48 ms 10.10.69.108
```

### **⚙️ How It Works:**

- Nmap uses **reverse TTL**: starts from **high TTL and decrements**, unlike traditional traceroute tools.

### **🚫 Limitations:**

- Routers configured **not to send ICMP Time Exceeded** won’t appear.
- On directly connected systems (same subnet), traceroute shows **only one hop**.

---

## **🔐 Permissions:**

- OS detection and traceroute often require **sudo/root**.