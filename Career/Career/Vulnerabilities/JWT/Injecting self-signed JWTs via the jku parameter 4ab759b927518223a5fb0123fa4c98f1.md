# Injecting self-signed JWTs via the jku parameter

Here are your notes, ready to paste into Notion:

---

## JWT `jku` Parameter Injection

- `jku` (JWK Set URL) = a header parameter that tells the server **where to fetch the verification key from**
- Instead of embedding the key in the token itself, the server makes an **outbound request to a URL** to get the key
- If the server doesn't validate which URLs it trusts → attacker can point it to **their own server**

---

## What is a JWK Set?

- A JSON object containing an **array of JWKs** (multiple keys)
- Server fetches this and finds the key matching the `kid` in the JWT header

```json
{
  "keys": [
    {
      "kty": "RSA",
      "e": "AQAB",
      "kid": "75d0ef47-af89-47a9-9061-7c02a610d5ab",
      "n": "o-yy1wpYmffgXBxhAUJzHH..."
    },
    {
      "kty": "RSA",
      "e": "AQAB",
      "kid": "d8fDFo-fS9-faS14a9-ASf99sa-7c1Ad5abA",
      "n": "fc3f-yy1wpYmffgXBx..."
    }
  ]
}
```

- Often publicly exposed at **`/.well-known/jwks.json`**

---

## The Vulnerability

- Misconfigured servers fetch keys from **any URL** in the `jku` parameter without checking if it's trusted
- Attacker hosts their own JWK Set → points `jku` to their server → server fetches attacker's public key → verifies attacker-signed token ✅

---

## Attack Flow

```
1. Generate your own RSA key pair
2. Host a JWK Set containing your public key (e.g. https://attacker.com/jwks.json)
3. Modify the JWT header:
      "jku": "https://attacker.com/jwks.json"
      "kid": "<your key's kid>"
4. Modify the payload (e.g. role → admin)
5. Sign the token with your RSA private key
6. Server fetches your JWK Set → verifies with your public key ✅
```

---

## Bypassing URL Whitelisting

- More secure servers only fetch from **trusted domains**
- But URL parsing discrepancies can sometimes bypass this:

| Bypass Technique | Example |
| --- | --- |
| Using `@` in URL | `https://trusted.com@attacker.com` |
| Subdomain abuse | `https://trusted.com.attacker.com` |
| URL fragments | `https://attacker.com#trusted.com` |
| SSRF techniques | Route through a server-side request the app trusts |

---

## `jwk` vs `jku` — Key Difference

|  | `jwk` | `jku` |
| --- | --- | --- |
| Key location | **Inside the token** | **Hosted on a URL** |
| Server action | Reads key from header | Makes outbound **HTTP request** to fetch key |
| Bypass needed | No URL filtering to bypass | May need to bypass **domain whitelist** |