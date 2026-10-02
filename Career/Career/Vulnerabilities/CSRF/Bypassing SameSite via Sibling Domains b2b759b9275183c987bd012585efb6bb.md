# Bypassing SameSite via Sibling Domains

**Core Concept:**

- **Sibling domains** = Same site, different origins
    - `app.example.com` & `api.example.com` = same site
    - `support.example.com` & `admin.example.com` = same site
- **SameSite restrictions don't protect** across sibling domains
- **Compromise one domain → Attack all others**

---

**Attack Flow:**

1. **Find vulnerability** in any sibling domain (XSS, open redirect, etc.)
2. **Exploit vulnerability** to make requests to target domain
3. **Requests are same-site** → Cookies included despite SameSite
4. **Full CSRF possible** across all sibling domains

---

**Example:**

- **Target:** `bank.example.com` (SameSite=Strict)
- **Vulnerable sibling:** `support.example.com` (XSS vulnerability)
- **Attack:**
    1. Inject XSS payload on `support.example.com`
    2. Make request to `bank.example.com/transfer`
    3. **Same-site request** → Bank cookies included
    4. Transfer executed

---

**Cross-Site WebSocket Hijacking (CSWSH)**

**What it is:** CSRF attack against WebSocket handshake

**How WebSockets Work:**

1. **HTTP handshake** (upgrade request)
2. **Upgrade to WebSocket** if handshake successful
3. **Persistent connection** established

**Vulnerability:**

- WebSocket handshake uses **HTTP cookies**
- If **no CSRF protection** on handshake → Vulnerable
- **SameSite restrictions apply** to handshake

**Attack:**

```html
<script>
  // Victim visits attacker page
  ws = new WebSocket('wss://target.com/chat');
  // Browser sends cookies (subject to SameSite)
  ws.onopen = () => {
    ws.send('{"type":"transfer","amount":1000}');
  };
</script>
```

---

**Impact:**

- **High risk:** One vulnerable domain compromises entire site
- **WebSocket attacks:** Can hijack real-time connections
- **Bypasses all SameSite levels** via sibling compromise

---

**Testing Methodology:**

**1. Sibling Domain Discovery:**

```bash
# Find all same-site domains
subfinder -d example.com
amass enum -d example.com
```

**2. Vulnerability Hunting:**

- XSS on any sibling domain
- Open redirects
- JSONP with callback attacks
- Post-message handlers

**3. WebSocket Testing:**

- Check WebSocket endpoints (`ws://`, `wss://`)
- Test handshake without CSRF token
- Verify if cookies are required/validated
- **Consider SameSite=Strict** for WebSocket cookies

---

**Key Takeaway:SameSite ≠ Domain Isolation**

- Security depends on **weakest sibling domain**
- **WebSockets vulnerable** to similar CSRF attacks
- **Complete site audit** required for real protection