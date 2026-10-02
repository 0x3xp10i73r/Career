# Nmap Host discovery using ARP

---

## **🛰️ Host Discovery with Nmap & ARP**

### **🔍 Why Discover Live Hosts?**

Avoid wasting time scanning offline targets.

---

### **🚀 Nmap Default Behavior**

| **User Type** | **Network Type** | **Method Used** |
| --- | --- | --- |
| **Privileged** | Local (Ethernet) | ARP requests |
| **Privileged** | External | ICMP Echo, ICMP Timestamp, TCP ACK (port 80), TCP SYN (port 443) |
| **Unprivileged** | External | TCP SYN to port 80 & 443 (3-way handshake) |

---

### **🧪 Nmap Host Discovery Only**

```
nmap -sn TARGETS
```

### **📡 ARP Scan with Nmap (Local Subnet Only)**

```
sudo nmap -PR -sn 10.10.X.X/24
```

- Sends ARP requests to all IPs in subnet.
- Gets MAC replies from online hosts.

---