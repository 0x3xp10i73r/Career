# Lab: Method-based access control can be circumvented

This lab implements access controls based partly on the HTTP method of requests. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.

To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.

1. Log in using the admin credentials.
2. Browse to the admin panel, promote `carlos`, and send the HTTP request to Burp Repeater.
3. Open a private/incognito browser window, and log in with the non-admin credentials.
4. Attempt to re-promote `carlos` with
the non-admin user by copying that user's session cookie into the
existing Burp Repeater request, and observe that the response says
"Unauthorized".
5. Change the method from `POST` to `POSTX` and observe that the response changes to "missing parameter".
6. Convert the request to use the `GET` method by right-clicking and selecting "Change request method".
7. Change the username parameter to your username and resend the request.

# HTTP Methods

### HTTP Methods

- **GET**:
    - Retrieves resources; parameters in URL (query string).
    - Test for: IDOR, parameter pollution, cache poisoning, reflected XSS, open redirects.
    - Often logged less strictly.
- **POST**:
    - Sends data in request body.
    - Test for: CSRF (missing tokens), XSS/SQLi in body params, mass assignment, file uploads.
    - Commonly used for sensitive actions (login, create).
- **PUT**:
    - Creates or fully replaces a resource (idempotent).
    - Test for: IDOR (overwrite others' resources), mass assignment, privilege escalation.
    - Often misconfigured to allow unauthorized updates.
- **PATCH**:
    - Partially updates a resource.
    - Similar risks to PUT: mass assignment, partial overwrites leading to logic flaws.
    - Frequently overlooked in authorization checks.
- **DELETE**:
    - Removes resources.
    - Test for: IDOR (delete others' data), missing authorization, lack of confirmation/CSRF protection.
    - High impact if unprotected.
- **HEAD**:
    - Like GET but only returns headers.
    - Test for: Information disclosure (server versions, headers), cache inconsistencies.
    - Useful for timing attacks or header-based vulns.
- **OPTIONS**:
    - Returns allowed methods on a resource.
    - Test for: Method exposure (reveals PUT/DELETE if enabled), CORS misconfiguration, unnecessary methods enabled.
    - Key for discovering over-permissive servers.
- **TRACE**:
    - Echoes back the request (for debugging).
    - Vulnerable to **HTTP Trace/Track attack** (XST - Cross-Site Tracing) for cookie theft via XSS.
    - Disable if enabled; high risk.
- **CONNECT**:
    - Used for tunneling (e.g., HTTPS proxies).
    - Rare in APIs; test for proxy abuse or SSRF if allowed.
- **Custom/Non-Standard Methods** (e.g., PROPFIND, WEBDAV methods):
    - Often enabled on misconfigured servers (IIS, Apache DAV).
    - Test for: WebDAV vulns, file access, privilege escalation.
- **General Testing Tips**:
    - Use Burp/ZAP to **force non-standard methods** (e.g., change POST to XYZ) → reveals method-based access control flaws.
    - Check if **restricted methods** (PUT/DELETE) lack proper auth/CSRF checks.
    - Test **method overriding** via headers: `X-HTTP-Method-Override`, `X-Method-Override`, or query param `?_method=DELETE`.
    - OWASP: Broken Access Control (A01:2021) often tied to improper method handling.
- **Tools**: Burp Suite (Repeater/Intruder), ZAP, curl `X METHOD`, Netcat for raw requests.

![image.png](Lab%20Method-based%20access%20control%20can%20be%20circumvente/image.png)

![image.png](Lab%20Method-based%20access%20control%20can%20be%20circumvente/image%201.png)

![image.png](Lab%20Method-based%20access%20control%20can%20be%20circumvente/image%202.png)

![image.png](Lab%20Method-based%20access%20control%20can%20be%20circumvente/image%203.png)