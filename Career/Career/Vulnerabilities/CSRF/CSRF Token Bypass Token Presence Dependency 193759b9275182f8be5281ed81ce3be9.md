# CSRF Token Bypass: Token Presence Dependency

Vulnerability:

Application validates CSRF token only when token parameter exists, not when it's missing.

How It Works:

- If `csrf_token` parameter present → validation occurs
- If `csrf_token` parameter absent → no validation
- Attacker removes entire token parameter, not just its value

Example:

Normal Request (with validation):

```
POST /email/change
csrf_token=abc123&email=user@normal.com
```

Malicious Request (bypass):

```
POST /email/change
email=evil@attacker.com
```

Attack Vector:

1. Remove `csrf_token` parameter entirely
2. Keep other parameters unchanged
3. Action executes without token validation

Testing Method:

1. Submit request with CSRF token → Should succeed
2. Submit request without CSRF token parameter → Check if still succeeds
3. If both work, vulnerability exists

Impact:

CSRF protection completely bypassed by omitting token parameter.

Note: Different from invalid token (which should fail) vs missing token (should also fail but doesn't).