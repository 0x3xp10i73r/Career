# Server-Side Request Forgery (SSRF) - Notes

## What is SSRF?

- A web security vulnerability that allows an attacker to cause the server-side application to make requests to unintended locations
- The attacker manipulates the server into making connections to:
    - Internal-only services within the organization's infrastructure
    - Arbitrary external systems

## Impact of SSRF Attacks

- Unauthorized actions or access to data within:
    - The vulnerable application itself
    - Other back-end systems the application can communicate with
- Potential for arbitrary command execution in some situations
- Malicious onward attacks that appear to originate from the organization hosting the vulnerable application
- Leakage of sensitive data like authorization credentials

## Common SSRF Attack Patterns

SSRF exploits trust relationships to escalate attacks from the vulnerable application.

### SSRF Against the Server

- Attacker causes the application to make HTTP requests back to its own server via loopback interface
- Uses hostnames like:
    - `127.0.0.1` (reserved IP for loopback adapter)
    - `localhost` (common name for same adapter)

### Example Scenario:

A shopping application checks stock by querying back-end APIs:

```
POST /product/stock HTTP/1.0
stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=6&storeId=1

```

Attack: Modify the request to target local server:

```
POST /product/stock HTTP/1.0
stockApi=http://localhost/admin

```

### Why This Works: Bypassing Access Controls

Applications often trust requests from the local machine because:

1. Access control checks might be implemented in a front-end component that bypasses checks for local requests
2. Disaster recovery mechanisms allow administrative access without login from local machine
3. Administrative interfaces might listen on different ports, unreachable directly by users but accessible via SSRF

## Key Characteristics

- Requests originating from local machine are often handled differently than ordinary requests
- This trust relationship makes SSRF a critical vulnerability
- Can lead to full administrative access bypassing normal authentication

## Takeaways

- SSRF turns server-side functionality into a proxy for attacker requests
- Focus on trust boundaries - any endpoint that fetches URLs is potentially vulnerable
- Internal services with weak/no authentication are primary targets via SSRF