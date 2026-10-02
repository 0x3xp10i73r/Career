# HTTP Host Header Notes

### What is the HTTP Host Header?

- The **Host header** is a **mandatory HTTP request header** since **HTTP/1.1**.
- It specifies **which domain name** the client wants to access.
- Example:
    
    ```jsx
    GET /web-security HTTP/1.1
    Host: portswigger.net
    ```
    
- Without the Host header, the server may not know **which website/application** the request is meant for.

---

### Why is the Host Header Important?

- Many websites today **share the same IP address**.
- The Host header tells the server **which back-end application** should handle the request.
- If the Host header is **missing or incorrect**, routing errors or security issues can occur.

---

### Why Is This Needed Today (But Not Before)?

- Earlier:
    - One IP address → One website
    - No ambiguity
- Now:
    - One IP address → **Multiple websites**
    - Common due to:
        - Cloud hosting
        - CDNs
        - Load balancers
        - IPv4 address exhaustion

---

### Common Scenarios Where Host Header Is Used

### 1. Virtual Hosting

- A **single web server** hosts **multiple websites**.
- All websites:
    - Share the **same IP address**
    - Have **different domain names**
- These websites are called **virtual hosts**.
- To users, they look like normal standalone websites.

---

### 2. Routing via an Intermediary

- Traffic passes through:
    - Load balancers
    - Reverse proxies
    - CDNs
- All domains resolve to the **same intermediary IP**.
- The intermediary uses the **Host header** to:
    - Identify the target website
    - Forward the request to the correct back-end server

---

### How the Host Header Solves the Problem

- The server receives a request at a shared IP.
- It reads the **Host header** to identify:
    - Which website
    - Which application
    - Which back-end server
- The request is then routed correctly.

---

### Easy Analogy

- **Apartment building**:
    - Street address = IP address
    - Apartment number/name = Host header
- Without the apartment number, mail can’t be delivered correctly.
- Similarly, without the Host header, HTTP requests can’t be routed properly.

---