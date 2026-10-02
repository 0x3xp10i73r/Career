# HTTP Host Header Attack

### What is an HTTP Host Header Attack?

- An **HTTP Host header attack** occurs when a website **trusts the Host header** without proper validation.
- Since the **Host header is user-controlled**, attackers can **modify it** to inject malicious values.
- Attacks where payloads are injected directly into the Host header are called **Host Header Injection** attacks.

---

### Why Is the Host Header Dangerous?

- Many web applications:
    - Do **not know their own domain**
    - Rely on the **Host header** to determine it dynamically
- Example:
    
    ```html
    <a href="https://$_SERVER['HOST']/support">Contact support</a>
    ```
    
- If the Host header is attacker-controlled:
    - The application may generate **malicious URLs**
    - Users may be redirected to attacker-owned domains

---

### Where Is the Host Header Commonly Used?

- Generating **absolute URLs** (emails, password resets)
- Internal communication between:
    - Load balancers
    - Reverse proxies
    - Back-end services
- Routing logic
- Cache keys

---

### What Can an Attacker Achieve?

If the Host header is not validated or escaped properly, it can lead to:

- **Web cache poisoning**
- **Business logic flaws**
- **Routing-based SSRF**
- **Server-side vulnerabilities**, such as:
    - SQL Injection
    - Command Injection
- **Malicious password reset links**
- **Open redirects**

---

### How Do HTTP Host Header Vulnerabilities Arise?

- Developers assume the Host header is **not user-controlled**
- This leads to:
    - Implicit trust
    - No validation or sanitization
- Attackers can easily modify the Host header using tools like:
    - Burp Suite
    - cURL
    - Postman

---

### Host Header Override via Other Headers

- Even if `Host` is validated, attackers may override it using alternative headers such as:
    - `X-Forwarded-Host`
    - `X-Host`
    - `Forwarded`
- Some servers **support these headers by default**
- Developers may be **unaware** they are enabled

---

### Root Cause of Most Host Header Issues

- Not always bad coding ❌
- Often caused by:
    - **Insecure server or proxy configuration**
    - Misconfigured:
        - Load balancers
        - Reverse proxies
        - CDNs
- Poor understanding of **third-party infrastructure settings**

---