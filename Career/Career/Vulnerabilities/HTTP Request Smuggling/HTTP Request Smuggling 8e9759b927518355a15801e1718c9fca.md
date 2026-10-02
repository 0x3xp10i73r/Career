# HTTP Request Smuggling

### Definition

- **HTTP Request Smuggling** occurs when **front-end and back-end servers interpret HTTP request boundaries differently**.
- Attackers exploit this to **inject a hidden request into the backend queue**.

---

### Why It Happens

- Applications use multiple servers:

```
Client → Front-end (Proxy / Load Balancer) → Back-end (App Server)
```

- Front-end forwards **multiple requests over the same connection**.
- If servers **disagree on where a request ends**, smuggling becomes possible.

---

### Root Cause

HTTP/1 allows two ways to define request length:

- **Content-Length**
- **Transfer-Encoding: chunked**

If both are present and parsed differently, **desynchronization occurs**.

---

### Types of Request Smuggling

**CL.TE**

- Front-end → Content-Length
- Back-end → Transfer-Encoding

**TE.CL**

- Front-end → Transfer-Encoding
- Back-end → Content-Length

**TE.TE**

- Both use Transfer-Encoding but **header obfuscation causes mismatch**.

---

### Impact

- Authentication bypass
- Session hijacking
- Cache poisoning
- Data theft

---

### Note

- Fully **HTTP/2 systems are not vulnerable**, but **HTTP/2 → HTTP/1 downgrading can introduce the issue**.