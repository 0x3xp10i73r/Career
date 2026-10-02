# Spoofing and Decoys

## **🕵️‍♂️ Nmap IP & MAC Spoofing, and Decoy Scans**

### **🔀 IP Spoofing**

- **What**: Forge the source IP in packets to a spoofed one.
- **Why**: Hide the real scanner’s IP or bypass basic filters.
- **Limitation**: Only works if **attacker can monitor replies** (e.g., on the same subnet, using packet capture tools).
- **Command Syntax**:

```bash
sudo nmap -e <interface> -Pn -S <spoofed_ip> <target_ip>
```

- e: Specify network interface.
- Pn: Skip ping discovery.
- S: Spoofed source IP.

---

### **🎭 MAC Address Spoofing**

- **What**: Forge the source MAC address.
- **When**: Only works on **same subnet (Layer 2)** (e.g., LAN, WiFi).
- **Command Syntax**:

```bash
sudo nmap --spoof-mac <mac_address> <target_ip>
```

- -spoof-mac 0A:1B:2C:3D:4E:5F — Spoof specific MAC.
- -spoof-mac 0 — Spoof a random MAC.

---

### **🧱 Decoy Scanning**

- **What**: Hide real IP among fake (decoy) IPs.
- **Why**: Obfuscate attacker’s identity during the scan.
- **Command Syntax**:

```bash
sudo nmap -D <decoy1>,<decoy2>,ME <target_ip>
```

- Example:

```bash
sudo nmap -D 10.10.0.1,10.10.0.2,ME 10.10.93.102
sudo nmap -D 10.10.0.1,10.10.0.2,RND,RND,ME 10.10.93.102
```

---

### **🧠 Pro Tips**

- These techniques are **evasion tactics** and useful in **red teaming** or **stealth scans**.
- You must have **root privileges** to perform spoofed or decoy scans.
- Always capture traffic with tcpdump or Wireshark if you’re spoofing IPs to view replies.