# Protected File Transfers

## Why Encrypt Before Transferring

- Intercepted data during pentest = **legal and professional liability**
- Sensitive data (NTDS.dit, creds, user lists, AD info) must be protected in transit
- Even if using HTTP (unencrypted channel), encrypting the **file itself** adds a safety layer
- Use **unique passwords per engagement** — never reuse encryption keys across clients

> ⚠️ Don't exfiltrate real PII/financial data. Use **dummy data** to test DLP controls instead.
> 

---

## Windows — AES Encryption (PowerShell)

Tool: `Invoke-AESEncryption.ps1`

Algorithm: AES-256-CBC

```powershell
# Step 1: Transfer script to target (any method), then import
Import-Module .\Invoke-AESEncryption.ps1

# Encrypt a file → creates file.txt.aes
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

# Decrypt
Invoke-AESEncryption -Mode Decrypt -Key "p4ssw0rd" -Path .\scan-results.txt.aes

# Encrypt a string
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Text "Secret Text"

# Decrypt a string
Invoke-AESEncryption -Mode Decrypt -Key "p4ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
```

Output: encrypted file gets `.aes` extension appended.

---

## Linux — OpenSSL Encryption

Tool: `openssl` (pre-installed on most Linux distros)

Algorithm: AES-256-CBC + PBKDF2

```bash
# Encrypt
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
# → prompts for password

# Decrypt
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
# → prompts for password
```

**Flag breakdown:**

| Flag | Purpose |
| --- | --- |
| `-aes256` | AES-256-CBC cipher |
| `-iter 100000` | 100,000 iterations → slows brute force |
| `-pbkdf2` | Stronger key derivation (PBKDF2) |
| `-in` | Input file |
| `-out` | Output file |
| `-d` | Decrypt mode |

---

## Workflow: Encrypt → Transfer → Decrypt

```
[Target]                          [Attacker]
  |                                   |
  | openssl enc → file.enc            |
  |---------------------------------->| (transfer via any method)
  |                                   | openssl enc -d → file
```

---

## Key Takeaways

- **Always encrypt sensitive files** before transfer, even over "secure" channels
- Use **unique strong passwords per client** — a leaked password from one job shouldn't compromise another
- `openssl` with `pbkdf2 -iter 100000` = significantly more resistant to brute force vs default
- After transfer, **verify file integrity** (md5sum) and then **delete encrypted copy** if no longer needed
- Prefer encrypted transport (SSH/SFTP/HTTPS) AND encrypted file = **defense in depth**