# TCP ACK, Window, and Custom Scan

## **✅ TCP ACK Scan (-sA)**

- **Purpose**: Map **firewall rules**, not discover open ports.
- **What it sends**: TCP packets with the **ACK** flag set.
- **Normal response**: RST from target (regardless of port being open or closed).
- **Behavior**:
    - **Unfiltered** → Packet reached the host, no firewall blocked it.
    - **Filtered** → No response (or dropped), likely filtered by a firewall.

🔎 **Use case**: Discover which ports the firewall allows **traffic to pass through**, not necessarily which ones have services listening.

```
sudo nmap -sA <target-ip>
```

---

## **✅ TCP Window Scan (-sW)**

- **Very similar to ACK scan**, but with a twist:
- **Key difference**: Analyzes the **TCP window size** of the RST packets.
    - Some OSes (like certain Windows versions) will send different window sizes for open vs. closed ports.
- **Behavior**:
    - **Closed** port → RST with zero window size
    - **Open** port → RST with a non-zero window size
- **Works only on some systems** due to TCP stack behavior differences.

```
sudo nmap -sW <target-ip>
```

---

## **✅ Custom TCP Flag Scan (--scanflags)**

- **Purpose**: Create custom scans to evade firewalls or IDS.
- **Example**: A packet with SYN + FIN + RST flags (unusual combo).
- **Useful for**:
    - IDS/IPS evasion
    - Behavior testing of custom stacks or hardware firewalls

```
sudo nmap --scanflags RSTSYNFIN <target-ip>
```

🎯 **Important**: You must interpret results manually — there’s **no automatic labeling** like “open” or “filtered.”

---

## **🔥 Pro Tip: Comparing the Scans**

| **Scan Type** | **Port State Result** | **Firewall Info?** | **Service Detection?** |
| --- | --- | --- | --- |
| -sA (ACK) | Unfiltered/Filtered | ✅ Yes | ❌ No |
| -sW (Window) | Closed/Filtered | ✅ Yes | ⚠️ Maybe (if OS allows) |
| --scanflags | Varies | ⚠️ Advanced | ⚠️ Manual analysis |

---

## **🧠 Final Notes**

- Just because a port appears **unfiltered** doesn’t mean it’s open — only that **firewall isn’t blocking it**.
- These techniques are gold for:
    - **Firewall bypass enumeration**
    - **Identifying “holes” in restrictive network perimeters**
    - **Planning stealthier scans** before using noisy SYN/Full Connect scans

Let me know if you’d like to:

- Write a sample nmap scan script
- Automate scans across subnets
- Build detection evasion techniques