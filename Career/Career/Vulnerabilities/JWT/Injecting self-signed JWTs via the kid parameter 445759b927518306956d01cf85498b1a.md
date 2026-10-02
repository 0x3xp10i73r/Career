# Injecting self-signed JWTs via the kid parameter

## JWT `kid` Parameter Injection

- `kid` (Key ID) = tells the server **which key to use** when verifying the JWT signature
- Keys are often stored in a **JWK Set** — server looks for the key matching the `kid` value
- The `kid` value is just an **arbitrary string** — no strict structure defined in the spec
- Can point to a **database entry, a file, or any arbitrary path**

---

## The Vulnerability — Directory Traversal via `kid`

- If the server uses `kid` to locate a **file on the filesystem**, it may be vulnerable to **path traversal**
- Attacker can manipulate `kid` to point to any file on the server

### Malicious Header Example

```json
{
  "kid": "../../path/to/file",
  "typ": "JWT",
  "alg": "HS256"
}
```

- Server reads the file at the traversed path and uses its **contents as the verification key**
- If attacker knows the file contents → they can **sign a forged token** with that same value

---

## Most Reliable Trick — `/dev/null`

- `/dev/null` is present on virtually **all Linux systems**
- Reading it always returns an **empty string**
- Sign the JWT with an **empty string as the secret** → server reads `/dev/null` → **signatures match** ✅

### Payload to Use

```json
{
  "kid": "../../../../../../dev/null",
  "typ": "JWT",
  "alg": "HS256"
}
```

- Sign the modified token with an **empty string** as the HMAC secret
- Server verifies using `/dev/null` contents (empty) → **valid signature**

---

## Why Symmetric Algorithms Make This Worse

| Algorithm | Key Type | Why Dangerous Here |
| --- | --- | --- |
| HS256 (symmetric) | Same key signs & verifies | Attacker just needs to **know the file contents** to forge a valid token |
| RS256 (asymmetric) | Private signs, public verifies | Harder — attacker would need a private key |

> ⚠️ This attack is most effective when the server uses **HS256** — because the same secret used to sign is used to verify, and attacker controls what that secret is
> 

---

## Attack Flow Summary

```
1. Find a JWT using kid parameter
2. Check if kid is vulnerable to path traversal
3. Set kid → "../../../../../../dev/null"
4. Modify payload (e.g. role → admin)
5. Sign token with empty string as secret
6. Server reads /dev/null → empty string → signatures match ✅
```