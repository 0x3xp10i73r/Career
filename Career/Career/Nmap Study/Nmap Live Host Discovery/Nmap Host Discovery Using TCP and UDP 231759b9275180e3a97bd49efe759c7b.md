# Nmap Host Discovery Using TCP and UDP

**🚨 Host Discovery via TCP/UDP Pings (Nmap)**

Beyond ICMP, Nmap supports TCP/UDP-based techniques for discovering online hosts. These are helpful when ICMP is blocked or filtered.

---

## **🔁 TCP SYN Ping -PS**

- Sends TCP **SYN** packets to target ports (default: 80).
- Expects **SYN-ACK** or **RST** as response → Host is *up*.
- Used **without completing full 3-way handshake** (root only).
- Multiple ports can be specified:
    - PS21, -PS21-25, -PS80,443,8080.

```
sudo nmap -PS -sn <TARGET_IP>/24
```

---

## **🪪 TCP ACK Ping -PA**

- Sends TCP **ACK** packets (default: port 80).
- Expects **RST** response → Host is *up*.
- Port specification is same as -PS:
    - PA22, -PA80,443.

```
sudo nmap -PA -sn <TARGET_IP>/24
```

---

## **🌐 UDP Ping -PU**

- Sends **UDP packets** to target port(s).
- Closed ports respond with **ICMP Port Unreachable (Type 3, Code 3)**.
- Open ports often **don’t reply at all**.
- Port options: -PU53, -PU123, -PU161,162.

```
sudo nmap -PU -sn <TARGET_IP>/24
```

---

## **📝 Summary Table**

| **Method** | **Flag** | **Protocol** | **Requires Root?** | **Notes** |
| --- | --- | --- | --- | --- |
| TCP SYN | -PS | TCP | ✅ Yes | Uses SYN, expects SYN-ACK/RST |
| TCP ACK | -PA | TCP | ✅ Yes | Uses ACK, expects RST |
| UDP Ping | -PU | UDP | ✅ Yes | Closed port → ICMP error |
| Masscan | N/A | TCP/UDP | ✅ Yes | Very fast, like Nmap on steroids |

---

Let me know if you’d like a printable version, flowchart, or Notion embed layout for quick review.