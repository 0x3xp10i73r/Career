# Understanding the TCP Header (RFC 793) for Nmap Scans

**📦 Understanding the TCP Header (RFC 793) for Nmap Scans**

## **🧠 Why TCP Header Matters in Port Scanning**

Nmap manipulates specific TCP flags to probe ports and determine their state (open, closed, filtered, etc.). To understand different scan types, you must understand the **TCP flags** used in the **first 24 bytes** of the TCP header.

---

## **🔍 Structure of a TCP Header (First 24 Bytes)**

| **Field** | **Size** | **Purpose** |
| --- | --- | --- |
| **Source Port** | 16 bits | Port of the sender |
| **Destination Port** | 16 bits | Port of the receiver (target service) |
| **Sequence Number** | 32 bits | Used to ensure reliable delivery |
| **Acknowledgment No.** | 32 bits | Confirms received packets |
| **Flags (Control Bits)** | 6 bits | Used to control the connection state |

---

## **🚩 TCP Flags Explained (Key for Nmap Scans)**

| **Flag** | **Full Name** | **Bit** | **Description** |
| --- | --- | --- | --- |
| **URG** | Urgent | 5 | Urgent pointer is significant. Data should be processed immediately. |
| **ACK** | Acknowledgment | 4 | Acknowledgment number is valid. Used to confirm data receipt. |
| **PSH** | Push | 3 | Ask the receiver to pass data to the application ASAP. |
| **RST** | Reset | 2 | Reset the connection. Often sent when no service is listening. |
| **SYN** | Synchronize | 1 | Initiate a TCP connection (start of 3-way handshake). |
| **FIN** | Finish | 0 | No more data to send — used to close a TCP connection. |

---

## **🛠️ Examples of Nmap Scans Using TCP Flags**

| **Scan Type** | **Nmap Option** | **TCP Flags Used** | **Purpose** |
| --- | --- | --- | --- |
| **SYN Scan** | -sS | SYN | Most popular stealth scan. Sends SYN, looks for SYN-ACK. |
| **ACK Scan** | -sA | ACK | Used to map firewall rules (port filtering). |
| **FIN Scan** | -sF | FIN | Stealthy scan. Many systems don’t respond to FIN on closed ports. |
| **Xmas Scan** | -sX | FIN, PSH, URG | Obscure scan using 3 flags to avoid detection. |
| **Null Scan** | -sN | (No flags) | Sends packets with no flags set — relies on RFC-compliant responses. |