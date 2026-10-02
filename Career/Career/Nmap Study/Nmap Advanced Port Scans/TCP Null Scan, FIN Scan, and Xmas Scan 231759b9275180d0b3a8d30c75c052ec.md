# TCP Null Scan, FIN Scan, and Xmas Scan

## **🔍 Nmap Stealth TCP Scans: Null, FIN, and Xmas**

### **1. Null Scan**

- **Command:** sudo nmap -sN 172.66.164.239
- **Flags:** No flags set (all 0)
- **Response behavior:**
    - **No response:** open|filtered
    - **RST received:** port is **closed**
- **Used to bypass:** simple/stateless firewalls
- **Limitation:** Doesn’t work reliably on Windows (based on RFC implementation)

---

### **2. FIN Scan**

- **Command:** sudo nmap -sF 172.66.164.239
- **Flags:** FIN flag only
- **Response behavior:**
    - **No response:** open|filtered
    - **RST received:** port is **closed**
- **Used to bypass:** firewalls that drop SYN but not FIN
- **Best against:** Unix/Linux targets (not Windows)

---

### **3. Xmas Scan**

- **Command:** sudo nmap -sX 172.66.164.239
- **Flags:** FIN + PSH + URG (like a lit-up Xmas tree 🎄)
- **Response behavior:**
    - **No response:** open|filtered
    - **RST received:** port is **closed**
- **Effective against:** stateless filtering systems
- **Blocked by:** stateful firewalls

---

### **⚠️ Notes:**

- All three scan types rely on **absence of RST** to guess open|filtered status.
- Not reliable against **stateful firewalls** or **Windows hosts** (due to RFC non-compliance).
- Requires **root privileges** (or sudo) to send raw TCP packets.

---

### **🛡 When to Use These Scans?**

Use them to:

- Evade detection by IDS/IPS or basic firewalls
- Identify open or filtered ports when traditional SYN scans (-sS) fail

Let me know if you want a comparison table or scan examples for bypassing firewalls.