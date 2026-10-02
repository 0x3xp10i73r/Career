# Weak JWT Secrets

## Brute-Forcing JWT Secret Keys

- Applies to **HS256 (HMAC + SHA-256)** and similar algorithms that use a single standalone string as the secret
- If the secret is weak or default → attacker can **forge any token**

---

## Why Weak Secrets Exist in the Wild

- Developers forget to change **default or placeholder secrets**
- Copy-paste code from online snippets with **hardcoded example secrets**
- Using short, guessable strings instead of a strong random secret

---

## Tool — Hashcat

- Best tool for brute-forcing JWT secrets
- Runs **locally** — no requests sent to the server → extremely fast
- Pre-installed on **Kali Linux**

### What You Need

- A valid signed JWT from the target
- A wordlist of well-known secrets (e.g. `jwt-secrets.txt` from SecLists)

---

### Command

```bash
hashcat -a 0 -m 16500 <jwt> <wordlist>
```

| Flag | Meaning |
| --- | --- |
| `-a 0` | Attack mode — straight wordlist |
| `-m 16500` | Hash type — JWT (HS256) |

### Output if Secret Found

```
<jwt>:<identified-secret>
```

### If Running Again

```bash
hashcat -a 0 -m 16500 <jwt> <wordlist> --show
```

> Always add `--show` flag on repeat runs — otherwise hashcat won't re-display cracked results
> 

---

## How Hashcat Works

```
For each word in wordlist:
  → HMAC-SHA256(header.payload, word)
  → Compare result to original signature
  → Match found? → output the secret ✅
```

---

## After Finding the Secret

1. Take your target JWT
2. Modify the **payload** (e.g. change `"role":"user"` → `"role":"admin"`)
3. Re-sign with the cracked secret → **valid forged token**
4. Use **Burp Suite JWT Editor** to re-sign and send

---