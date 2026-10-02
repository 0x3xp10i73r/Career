# Fragmented Packets

## **🔥 Firewall vs 🛡️ IDS (Intrusion Detection System)**

| **Feature** | **Firewall** | **IDS** |
| --- | --- | --- |
| Purpose | Allow/block network traffic | Detect suspicious/malicious traffic patterns |
| Inspection | IP & Transport Layer headers | Deep Packet Inspection (including payload) |
| Action | Permit or deny traffic | Alerts/logs suspicious activity (no blocking) |
| Mode | Active filtering | Passive detection |

---

## **🕵️‍♀️ Evading Detection with Nmap**

Firewalls and IDSs often detect standard Nmap scan patterns. To avoid this, you can **fragment packets** or modify them to evade basic filtering mechanisms.

---

## **🔗 Fragmented Packets with**

## **f**

- **Why**: Make packets harder to analyze by splitting them into smaller parts.
- **How**: Use the -f flag to fragment into 8-byte segments.

```
sudo nmap -sS -p80 -f 10.20.30.144
```

- **Double Fragmentation (-ff)**: Split into 16-byte fragments.

```
sudo nmap -sS -p80 -f -f 10.20.30.144
```

- **Custom MTU Fragment Size**:

```
sudo nmap -sS -p80 --mtu 32 10.20.30.144
```

- 🔸 Note: --mtu value must be a multiple of **8**.

---

## **📦 TCP Header Fragmentation Example**

- TCP header size: **24 bytes**
- With -f:
    - Split into **3 fragments** (8 + 8 + 8)
- With -ff:
    - Split into **2 fragments** (16 + 8)

These are reconstructed on the target but may **confuse or bypass firewalls/IDS** that don’t reassemble fragmented packets.

---

## **🎭 Padding Packets with**

## **-data-length**

- **Purpose**: Make packets look “normal” or alter signature.
- **Usage**:

```
sudo nmap -sS -p80 --data-length 50 10.20.30.144
```

- **Effect**: Appends 50 random bytes to the packet.

---

## **✅ When is this useful?**

- Target has **stateless** firewalls.
- Bypassing **signature-based IDS**.
- Obfuscating **packet length and behavior**.