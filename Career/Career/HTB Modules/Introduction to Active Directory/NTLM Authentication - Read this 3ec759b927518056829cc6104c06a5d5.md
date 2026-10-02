# NTLM Authentication - Read this

## Hash/Protocol Comparison

| Hash/Protocol | Crypto Type | Mutual Auth | Trusted Third Party | Notes |
| --- | --- | --- | --- | --- |
| **LM** | Symmetric | ❌ No | DC | Obsolete, max 14 chars, disabled since Vista |
| **NTLM (NT hash)** | Symmetric | ❌ No | DC | MD4 of UTF-16-LE password. Vulnerable to PtH |
| **NTLMv1** | Symmetric | ❌ No | DC | Uses NT + LM hash. Weaker, can be relayed |
| **NTLMv2** | Symmetric | ❌ No | DC | Default since Server 2000. Stronger than v1 |
| **Kerberos** | Symmetric + **Asymmetric** | ✅ **Yes** | KDC/DC | Ticket-based, preferred protocol |

---

## LM Hash — Why It's Weak

- Max **14 characters**, **not case sensitive** (uppercased before hashing)
- Password split into **two 7-character chunks** → hashed separately with DES
- Only need to brute-force **7 chars twice** instead of 14
- Password ≤ 7 chars = second half is always the same value (visually identifiable)
- Disabled by default since **Windows Vista / Server 2008**
- Format: `299bd128c1101fd6`

---

## NT Hash (NTLM)

Algorithm: `MD4(UTF-16-LE(password))`

**Full NTLM hash format:**

```
Rachel:500:aad3c435b514a4eeaad3b935b51304fe:e46b9e548fa0d122de7f59fb6d48eaa2:::
```

| Part | Value |
| --- | --- |
| Username | `Rachel` |
| RID | `500` (Administrator RID) |
| LM Hash | `aad3c435b514a4eeaad3b935b51304fe` |
| **NT Hash** | `e46b9e548fa0d122de7f59fb6d48eaa2` ← this is what matters |

**Entire 8-char NTLM keyspace crackable in under 3 hours on GPU**

**Pass-the-Hash attack:**

```bash
crackmapexec smb 10.129.41.19 -u rachel -H e46b9e548fa0d122de7f59fb6d48eaa2
```

---

## NTLMv1 vs NTLMv2

| Feature | NTLMv1 | NTLMv2 |
| --- | --- | --- |
| Uses | NT + LM hash | NT hash only |
| Challenge | 8-byte server challenge | 8-byte server + 8-byte client challenge |
| Response | 24-byte DES response | HMAC-MD5 responses (more complex) |
| Pass-the-Hash | ❌ Cannot | ❌ Cannot |
| Crack offline | Easier | Harder |
| Relay attacks | Vulnerable | More hardened |
| Captured by | Responder | Responder |
| Hashcat mode | 5500 | **5600** |

**NTLMv1 hash example:**

```
u4-netntlm::kNS:338d08f8e26de93300000000000000000000000000000000:9526fb8c23a90751...
```

**NTLMv2 hash example:**

```
admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958dac6:5c783031...
```

---

## Domain Cached Credentials (DCC / MSCache2)

- Used when DC is **unreachable** (network outage etc.)
- Stores last **10** domain user hashes in registry: `HKEY_LOCAL_MACHINE\SECURITY\Cache`
- **Cannot** be used for pass-the-hash attacks
- **Very slow** to crack even with GPU — only worth targeting if password is weak
- Require **local admin access** to extract

**Format:** `$DCC2$10240#bjones#e4e938d12fe5974dc42a90120bd9c90f`

**Hashcat mode:** 2100

---

## Attack Capability Summary

| Hash Type | Crack Offline | Pass-the-Hash | Relay | Notes |
| --- | --- | --- | --- | --- |
| LM | ✅ Easy | ❌ | ❌ | 7-char chunks |
| NT (NTLM) | ✅ Moderate | ✅ **Yes** | ❌ | Primary PtH target |
| NTLMv1 | ✅ Easier | ❌ | ✅ | Captured by Responder |
| NTLMv2 | ✅ Harder | ❌ | ✅ | Captured by Responder, mode 5600 |
| DCC/MSCache2 | ⚠️ Very slow | ❌ | ❌ | Local admin needed to extract |

---

## Question Answers

| Question | Answer |
| --- | --- |
| Protocol with symmetric + asymmetric cryptography | **Kerberos** |
| Missing NTLM message after Negotiate, Challenge | **Authenticate** |
| DCC default hashes saved per host | **10** |