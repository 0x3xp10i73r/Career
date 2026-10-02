# Nmap Host Discovery using ICMP

# **🔍 ICMP-Based Host Discovery with Nmap**

Discovering live hosts on a network using ICMP methods can help avoid wasting time on offline targets. Below are different ICMP-based Nmap techniques and their behaviors.

---

## **🧪 1. ICMP Echo Request (-PE)**

- Sends **ICMP Echo Request (Type 8)** to every IP.
- Expects **ICMP Echo Reply (Type 0)**.
- Common Windows Firewall or other firewalls may **block** these.
- Add -sn to skip port scanning.

```
sudo nmap -PE -sn <TARGET_IP>/24
```

### **🔍 Example Output:**

- Shows Host is up with MAC address (if on same subnet).
- If **on a different subnet**, MACs are not shown.

---

## **⏱️ 2. ICMP Timestamp Request (-PP)**

- Sends **ICMP Timestamp Request (Type 13)**.
- Waits for **Timestamp Reply (Type 14)**.
- Useful when Echo Requests are blocked.

```
sudo nmap -PP -sn <TARGET_IP>/24
```

### **🔍 Example Output:**

- Similar to -PE, returns host status if target responds.

---

## **🧭 3. ICMP Address Mask Request (-PM)**

- Sends **ICMP Address Mask Request (Type 17)**.
- Expects **Reply (Type 18)**.
- Often blocked by firewalls or unsupported by modern OS.

```
sudo nmap -PM -sn <TARGET_IP>/24
```

### **⚠️ Example Output:**

- **Zero hosts detected** despite them being online.
- Proves some scan types can be blocked or filtered.

---

## **📝 Key Takeaways**

- ✅ **Use -PE** when ICMP Echo is allowed.
- ✅ **Fallback to -PP** if Echo is blocked.
- ❌ **Avoid relying on -PM**; rarely supported.
- 🔄 If one scan fails, try another — **multiple discovery methods** increase accuracy.
- 🔐 Firewalls or OS configurations may block specific ICMP types.

---

Let me know if you want a visual version or want to add a section for ARP-based or TCP-based discovery too!