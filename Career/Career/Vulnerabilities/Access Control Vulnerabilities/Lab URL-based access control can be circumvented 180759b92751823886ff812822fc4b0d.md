# Lab: URL-based access control can be circumvented

This website has an unauthenticated admin panel at /admin, but a front-end system has been configured to block external access to that path. However, the back-end application is built on a framework that supports the X-Original-URL header.

To solve the lab, access the admin panel and delete the user carlos.

# Some Notes

### X-Original-URL in VAPT (Vulnerability Assessment & Penetration Testing)

- **Vulnerability Class**: Improper Access Control / Request Smuggling / Header Injection
- **Testing Methodology**:
    1. Intercept legitimate request using proxy (Burp Suite, ZAP)
    2. Add/inject header: `X-Original-URL: /restricted/path`
    3. Keep original request path benign (e.g., GET /)
    4. Send and observe if response corresponds to the injected path
    5. Test variations: relative paths, encoded values (%2fadmin), full URLs
- **Example Exploit Request**:
    
    ```
    GET / HTTP/1.1
    Host: target.com
    X-Original-URL: /admin/dashboard
    
    ```
    

![image.png](Lab%20URL-based%20access%20control%20can%20be%20circumvented/image.png)

![image.png](Lab%20URL-based%20access%20control%20can%20be%20circumvented/image%201.png)

![image.png](Lab%20URL-based%20access%20control%20can%20be%20circumvented/image%202.png)

![image.png](Lab%20URL-based%20access%20control%20can%20be%20circumvented/image%203.png)

![image.png](Lab%20URL-based%20access%20control%20can%20be%20circumvented/image%204.png)