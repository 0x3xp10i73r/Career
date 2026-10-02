# Testing for HTTP Host Header Vulnerabilities

## Core Testing Idea

- Check whether you can **change the Host header** and still:
    - Reach the target application
    - Influence the response or server behavior
- If yes → the Host header becomes an **attack surface**

---

## Step 1: Supply an Arbitrary Host Header

- Replace the original Host header with an **unexpected or fake domain**
- Example:
    
    ```
    Host: attacker-domain.com
    ```
    
- Observe:
    - Does the request still reach the application?
    - Does the response change?

---

### Possible Outcomes

- ✅ Application still loads
→ Server may have a **default/fallback virtual host**
- ❌ “Invalid Host header” error
→ Likely strict routing (common with CDNs)

If blocked → move to **bypass techniques**

---

## Step 2: Check for Flawed Host Validation

### Common Validation Checks

- Host header compared with:
    - TLS SNI value
    - Whitelisted domains

⚠️ Validation does **not guarantee safety**

---

### Bypass Techniques

### 1. Inject Payload via Port

- Some servers validate only the **domain**, not the port
- Example:
    
    ```
    Host: vulnerable-site.com:evil-payload
    ```
    

---

### 2. Abuse Weak Subdomain Matching

- Validation allows arbitrary subdomains
- Example:
    
    ```
    Host: attacker-vulnerable-site.com
    ```
    

---

### 3. Use a Compromised Subdomain

- If you control a subdomain:
    
    ```
    Host: hacked.vulnerable-site.com
    ```
    

---

## Step 3: Send Ambiguous Requests

- Different components (proxy, backend, app) may:
    - Parse Host header differently
- Goal: Make **frontend and backend disagree**

---

### Technique 1: Duplicate Host Headers

```
Host: vulnerable-site.com
Host: attacker-site.com
```

- Possible behavior:
    - Frontend uses first Host
    - Backend uses second Host
- Result: Payload reaches backend logic

---

### Technique 2: Absolute URL in Request Line

```
GET <https://vulnerable-site.com/> HTTP/1.1
Host: attacker-site.com
```

- Some systems prioritize:
    - Request line
    - Others prioritize Host header
- Can cause routing inconsistencies

---

### Technique 3: Line Wrapping (Header Indentation)

```
    Host: attacker-site.com
Host: vulnerable-site.com

```

- Some servers:
    - Treat indented header as continuation
    - Others ignore it
- Can bypass duplicate Host validation

---

## Step 4: Inject Host Override Headers

- Even if `Host` is protected, other headers may override it

### Common Host Override Headers

- `X-Forwarded-Host`
- `X-Host`
- `X-Forwarded-Server`
- `X-HTTP-Host-Override`
- `Forwarded`

Example:

```
Host: vulnerable-site.com
X-Forwarded-Host: attacker-site.com

```

---

### Why This Works

- Used by proxies/load balancers to pass original Host
- Many frameworks **trust these headers by default**
- Validation often applied only to `Host`, not overrides

---

### Automation Tip (Burp)

- Use **Param Miner → Guess Headers**
- Automatically tests for supported override headers

---

## Root Cause of These Issues

- Assumption: Host header is not user-controlled ❌
- Insecure configuration of:
    - Load balancers
    - Reverse proxies
    - Third-party services
- Developers unaware of **default-enabled headers**

---