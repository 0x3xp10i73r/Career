# Nmap Live Host Discovery

### **🚀 Nmap Default Behavior**

| **User Type** | **Network Type** | **Method Used** |
| --- | --- | --- |
| **Privileged** | Local (Ethernet) | ARP requests |
| **Privileged** | External | ICMP Echo, ICMP Timestamp, TCP ACK (port 80), TCP SYN (port 443) |
| **Unprivileged** | External | TCP SYN to port 80 & 443 (3-way handshake) |

[Nmap Host discovery using ARP](Nmap%20Live%20Host%20Discovery/Nmap%20Host%20discovery%20using%20ARP%20231759b9275180689945f4d4fe1de033.md)

[Nmap Host Discovery using ICMP](Nmap%20Live%20Host%20Discovery/Nmap%20Host%20Discovery%20using%20ICMP%20231759b9275180d08d9bf1de69a4df10.md)

[Nmap Host Discovery Using TCP and UDP](Nmap%20Live%20Host%20Discovery/Nmap%20Host%20Discovery%20Using%20TCP%20and%20UDP%20231759b9275180e3a97bd49efe759c7b.md)

# **🧭 Nmap Host Discovery Summary (No Port Scanning)**

Any response = target is **online** ✅

## **🔍 Command Summary**

| **Scan Type** | **Example Command** | **Notes** |
| --- | --- | --- |
| **ARP Scan** | sudo nmap -PR -sn MACHINE_IP/24 | Local subnet only |
| **ICMP Echo Scan** | sudo nmap -PE -sn MACHINE_IP/24 | May be blocked by firewalls |
| **ICMP Timestamp Scan** | sudo nmap -PP -sn MACHINE_IP/24 | Alternative to Echo, less common |
| **ICMP Address Mask Scan** | sudo nmap -PM -sn MACHINE_IP/24 | Rarely used, often blocked |
| **TCP SYN Ping Scan** | sudo nmap -PS22,80,443 -sn MACHINE_IP/30 | SYN to known ports, root required |
| **TCP ACK Ping Scan** | sudo nmap -PA22,80,443 -sn MACHINE_IP/30 | Sends ACKs, expects RST |
| **UDP Ping Scan** | sudo nmap -PU53,161,162 -sn MACHINE_IP/30 | Expects ICMP Port Unreachable response |

---

## **⚙️ Key Nmap Options**

| **Option** | **Purpose** |
| --- | --- |
| -n | Skip DNS resolution (faster) |
| -R | Force reverse DNS lookup for all targets |
| -sn | Ping scan only (no port scan) |

> ✅ Use -sn to
> 
> 
> **only detect live hosts**
>