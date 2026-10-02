# CSRF Token Bypass: Request Method Dependency

**Vulnerability:**

CSRF token validation depends on HTTP request method.

**How It Works:**

- Application validates tokens for **POST** requests
- Application **skips validation** for **GET** requests
- Attacker switches from POST to GET to bypass protection

**Example:**

```bash
POST /email/change HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Cookie: session=abc123

email=new@evil.com&csrf=valid_token  ← Token validated
```

```bash
GET /email/change?email=pwned@evil.com HTTP/1.1
Cookie: session=abc123                ← No token required!
```

**Attack Vector:**

- Attacker crafts malicious GET request URL
- Victim visits URL while authenticated
- Action executes without CSRF token

**Testing Method:**

1. Find POST endpoints with CSRF tokens
2. Change request method to GET
3. Move parameters to URL query string
4. Check if action still executes

**Impact:**

Full CSRF attack possible despite token implementation.