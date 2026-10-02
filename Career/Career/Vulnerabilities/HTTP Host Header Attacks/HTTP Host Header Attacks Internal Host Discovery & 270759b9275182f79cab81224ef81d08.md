# HTTP Host Header Attacks: Internal Host Discovery & SSRF Techniques

---

## Accessing Internal Websites via Virtual Host Brute-Forcing

### Background

- Companies sometimes host:
    - **Public websites**
    - **Private/internal websites**
- Both may run on the **same physical server**
- Server usually has:
    - **Public IP** (internet-facing)
    - **Private IP** (internal network)

---

### Why DNS Alone Is Not Enough

- Internal sites may resolve to **private IPs**
- Example:
    
    ```
    www.example.com      → 12.34.56.78
    intranet.example.com → 10.0.0.132
    ```
    
- Internal hostnames may:
    - Not appear in public DNS
    - Still be accessible via **virtual hosting**

---

### Key Concept: Virtual Host Access

- If you can reach the server:
    - You can often access **any virtual host** on it
- Requirement:
    - Guess or discover the **hostname**
- Host header controls **which virtual host is served**

---

### Attacker Approach

### 1. Known Internal Hostname

- If leaked via:
    - Error messages
    - JavaScript
    - Config files
- Simply send:
    
    ```
    Host: intranet.example.com
    ```
    

---

### 2. Virtual Host Brute-Forcing

- If hostname is unknown:
    - Use **Burp Intruder**
    - Brute-force common subdomains:
        - intranet
        - admin
        - internal
        - dev
        - staging
- Target:
    - Host header field
- Goal:
    - Identify valid internal virtual hosts

---

## Routing-Based SSRF via Host Header

### What Is Routing-Based SSRF?

- A form of SSRF that:
    - Exploits **load balancers**
    - Exploits **reverse proxies**
- Instead of application logic, it abuses:
    - **Request routing decisions**

---

### How It Works

- Intermediary systems:
    - Receive public traffic
    - Forward requests internally
- If they trust the **Host header**:
    - Attacker can control routing
- Result:
    - Requests forwarded to **arbitrary systems**

---

### Why This Is High Impact

- Load balancers:
    - Are internet-facing
    - Have access to internal networks
- Successful exploitation can:
    - Expose internal services
    - Turn infrastructure into an SSRF gateway

---

### Detecting Routing-Based SSRF

### Using Burp Collaborator

- Set Host header to Collaborator domain:
    
    ```
    Host: attacker-collaborator.com
    ```
    
- Monitor for:
    - DNS lookups
    - HTTP requests
- If observed:
    - An intermediary is resolving the Host header
    - Routing manipulation is possible

---

## Accessing Internal Systems

### Next Steps After Confirmation

- Try routing requests to:
    - Internal hostnames
    - Private IP addresses
- Sources of internal IPs:
    - Application leaks
    - Internal subdomain resolution
    - Brute-forcing private IP ranges

---

### Common Private IP Ranges

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

---

## CIDR Notation (Simple Explanation)

### IPv4 Basics

- IPv4 address:
    - 4 octets (0–255)
    - Example: `192.168.1.10`

---

### What CIDR Means

- Format:
    
    ```
    base_ip / number_of_fixed_bits
    ```
    
- Example:
    
    ```
    10.0.0.0/8
    ```
    
- Meaning:
    - First 8 bits are fixed
    - Range includes:
        - `10.0.0.0` → `10.255.255.255`

---

### Why CIDR Matters in Attacks

- Helps attackers:
    - Systematically scan internal IPs
    - Identify reachable internal services
- Used heavily in:
    - SSRF exploitation
    - Internal network discovery

---

## Key Takeaways

- Virtual host brute-forcing can expose **internal websites**
- Host header can be abused for:
    - Virtual host discovery
    - Routing-based SSRF
- Load balancers and proxies are **high-value targets**
- Private IP ranges should **never be routable via Host header input**

---

If you want, I can:

- Create a **Burp Intruder wordlist**
- Show **real-world exploitation flow**
- Map this to **OWASP SSRF categories**
- Turn this into a **step-by-step lab** 🧪🔐