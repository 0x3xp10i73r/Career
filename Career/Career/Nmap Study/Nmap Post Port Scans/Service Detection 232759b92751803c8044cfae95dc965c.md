# Service Detection

# **🔍 Nmap Service and Version Detection (-sV)**

## **🧭 Purpose**

To detect **services and versions** running on open ports. This helps in identifying:

- The exact **service software**
- The **version** number
- Potentially vulnerable versions

---

## **🛠️ Key Nmap Options**

| **Option** | **Description** |
| --- | --- |
| -sV | Enables service and version detection |
| --version-intensity <0–9> | Controls how deeply Nmap probes the service**0 = lightest**, **9 = most thorough** |
| --version-light | Same as --version-intensity 2 |
| --version-all | Same as --version-intensity 9 |

> 🔐 Note: Running -sV disables stealth SYN scan -sS because version detection
> 
> 
> **requires full TCP connection**
> 

---

## **🧪 Example Output**

```
sudo nmap -sV 10.10.69.108
```

```bash
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
25/tcp  open  smtp    Postfix smtpd
80/tcp  open  http    nginx 1.6.2
110/tcp open  pop3    Dovecot pop3d
111/tcp open  rpcbind 2-4 (RPC #100000)
```

- **Service column**: Guessed from port number
- **Version column**: Determined from actual service response/banner
- **MAC Address** and **Service Info** may also be reported

---

## **✅ Notes**

- Requires **root privileges**, hence use sudo
- Use when you want **deeper insight** into detected services
- Combine with other flags (e.g., -p-, -T4, -oN output.txt) for more tailored scans