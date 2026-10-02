# CSRF Token Bypass: Token Bound to Wrong Cookie

**Vulnerability:**

CSRF token is tied to **a cookie, but not the session cookie**. Two different frameworks handle sessions and CSRF separately.

**How It Works:**

- **Session cookie:** `session=abc123` (for authentication)
- **CSRF cookie:** `csrfKey=xyz789` (for CSRF protection)
- Token validated against `csrfKey`, not session
- Attacker can set victim's `csrfKey` cookie

**Attack Requirements:**

1. **Cookie-setting vulnerability** elsewhere in application
2. Attacker can force victim's browser to accept their `csrfKey`
3. Attacker uses their own valid token with victim's hijacked `csrfKey`

**Attack Flow:**

1. **Attacker logs in** → Gets: `session=attacker_sess`, `csrfKey=attacker_key`, `token=attacker_token`
2. **Exploit cookie injection** → Sets victim's `csrfKey=attacker_key`
3. **Craft CSRF attack** → Uses `token=attacker_token`
4. **Victim executes** → Browser sends: `session=victim_sess`, `csrfKey=attacker_key`, `token=attacker_token`
5. **Validation passes** → Token matches `csrfKey` (both attacker's)

**Example Headers:**

```
Cookie: session=victim_session; csrfKey=attacker_key
Body: csrf=attacker_token&email=evil.com

```

**Testing Method:**

1. Check if separate CSRF cookie exists
2. Verify token validation uses CSRF cookie, not session
3. Find cookie-setting vulnerabilities (XSS, header injection, etc.)

**Impact:**

CSRF bypass if cookie injection exists anywhere on same domain.

**Defense Required:**

Token must be tied to **session cookie**, not separate CSRF cookie.