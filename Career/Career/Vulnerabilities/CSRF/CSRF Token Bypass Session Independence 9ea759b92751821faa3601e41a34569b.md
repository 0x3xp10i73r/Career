# CSRF Token Bypass: Session Independence

Vulnerability:

CSRF tokens are not tied to user sessions - application maintains a global token pool.

How It Works:

- Application issues tokens without linking to specific sessions
- Any valid token from any session is accepted
- Tokens stored in global pool/database

Attack Flow:

1. Attacker logs in to their own account
2. Attacker obtains valid CSRF token from their session
3. Attacker crafts malicious request using their token
4. Victim executes request with attacker's token
5. Application accepts token (valid in global pool)x
6. Action performed on victim's account

Example:

- Attacker token: `csrf=abc123` (from attacker's session)
- Victim session: `session=victim_cookie`
- Request: `POST /change-email?email=evil.com&csrf=abc123`
- Accepted! (Token valid but from wrong session)

Testing Method:

1. Log in as User A, capture CSRF token
2. Log in as User B (different session)
3. Use User A's token in User B's request
4. Check if action succeeds

Impact:

Full CSRF attack possible - attacker can reuse their own tokens on victims.

Defense Required:

Tokens must be cryptographically tied to specific user sessions.