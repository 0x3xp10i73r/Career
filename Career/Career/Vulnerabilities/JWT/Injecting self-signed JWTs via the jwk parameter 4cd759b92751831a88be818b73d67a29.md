# Injecting self-signed JWTs via the jwk parameter

## Attack 1 — Injecting Self-Signed JWTs via `jwk`

### How It Should Work

- Server uses a **whitelist of trusted public keys** to verify signatures
- The `jwk` parameter is meant to help the server identify the right key

### The Vulnerability

- Misconfigured servers accept **any key embedded in the `jwk` header** — including attacker-supplied ones
- Attacker can sign a modified token with their **own RSA private key** and embed the matching public key in the header
- Server verifies with the embedded key → **signature is valid** ✅

---

### Example Malicious JWT Header

```json
{
  "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
  "typ": "JWT",
  "alg": "RS256",
  "jwk": {
    "kty": "RSA",
    "e": "AQAB",
    "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
    "n": "yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9m"
  }
}
```

- `jwk` contains **attacker's own public key**
- Token is signed with the **matching private key**
- Server uses the embedded key to verify → passes ✅

---

### Exploit Steps — Burp Suite (JWT Editor Extension)

1. Go to **JWT Editor Keys** tab → Generate new **RSA key**
2. Send a request containing a JWT to **Burp Repeater**
3. Switch to the **JSON Web Token** tab in the message editor
4. **Modify the payload** (e.g. change role to admin)
5. Click **Attack** → select **Embedded JWK** → select your RSA key
6. Send the request and observe the response

> 💡 The extension automatically matches the `kid` in the header to your embedded key — if doing it manually, make sure `kid` values match in both the header and the `jwk` object
> 

---

### Why This Works

```
Normal flow:   Server verifies JWT using its own trusted public key
Attack flow:   Server verifies JWT using attacker's public key (embedded in token)
               Attacker signed it with matching private key → verification passes ✅
```