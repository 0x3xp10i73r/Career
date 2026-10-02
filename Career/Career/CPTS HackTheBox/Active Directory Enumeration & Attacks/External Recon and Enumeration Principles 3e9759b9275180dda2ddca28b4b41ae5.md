# External Recon and Enumeration Principles

## Why External Recon Matters

- Validate scoping document information
- Find leaked credentials that can access VPN/external services
- Discover infrastructure not disclosed by client
- Build username lists for password spraying
- Identify tech stack and defenses before going internal

---

## What to Look For

| Data Point | What to Find |
| --- | --- |
| **IP Space** | ASN, netblocks, cloud providers, DNS records |
| **Domain Info** | Subdomains, mail servers, DNS servers, VPN portals, defenses |
| **Schema Format** | Email format (first.last), AD username format, password policy hints |
| **Data Disclosures** | PDFs/docs with metadata, intranet links, GitHub credentials |
| **Breach Data** | Leaked usernames, passwords, hashes |

---

## Where to Look & Tools

| Resource | Tool/Site |
| --- | --- |
| ASN / IP blocks | [BGP.he.net](http://bgp.he.net/) (Hurricane Electric BGP Toolkit), IANA, ARIN, RIPE |
| DNS records | [ViewDNS.info](http://viewdns.info/), Domaintools, PTRArchive, `nslookup`, `dig` |
| Email/username discovery | LinkedIn, company website contact pages |
| Username generation | `linkedin2username` |
| File hunting | Google Dorks |
| Breach data | HaveIBeenPwned, Dehashed |
| Code/cloud leaks | GitHub (Trufflehog), GrayHat Warfare, AWS S3, Azure Blob |
| Social media | LinkedIn, Twitter, Facebook, Glassdoor, Indeed |

---

## Google Dorks Cheat Sheet

```
# Find PDFs hosted on target domain
filetype:pdf inurl:inlanefreight.com

# Find email addresses on target site
<intext:"@inlanefreight.com>" inurl:inlanefreight.com

# Find exposed documents
filetype:docx inurl:inlanefreight.com
filetype:xlsx inurl:inlanefreight.com
filetype:pptx inurl:inlanefreight.com

# Find login portals
inurl:inlanefreight.com intext:"login"

# Find cloud storage
site:s3.amazonaws.com "inlanefreight"
site:blob.core.windows.net "inlanefreight"
```

---

## DNS Validation

```bash
# Check nameservers
nslookup ns1.inlanefreight.com
nslookup ns2.inlanefreight.com

# Full DNS lookup
dig any inlanefreight.com

# Reverse IP lookup (viewdns.info or command line)
# Shows other domains hosted on same IP
```

---

## Enumeration Process (Passive → Active)

```
BGP/ASN lookup → DNS records → ViewDNS validation
    → Google Dorks (files, emails)
        → Social media / LinkedIn / Job postings
            → Breach data (Dehashed, HIBP)
                → Username list building (linkedin2username)
                    → Target-specific wordlist for spraying
```

---

## Username & Credential Hunting

```bash
# Scrape LinkedIn for usernames
python3 linkedin2username.py -u <your_linkedin> -c "Inlanefreight"
# Outputs: flast, first.last, f.last formats

# Search Dehashed for breach data
python3 dehashed.py -q inlanefreight.local -p
# Returns: email, username, plaintext password, hash
```

**Email format from contact page** → derive AD username format

- `john.smith@inlanefreight.com` → AD username likely `j.smith` or `john.smith` or `jsmith`

---

## What Job Postings Reveal (Example)

- SharePoint 2013 + 2016 → upgraded in place → old vuln versions may still exist
- Azure mentioned → cloud presence, possible Azure AD
- Specific software = known CVEs to research
- Tech stack = frameworks, databases, languages in use

---

## Important Reminders

- Smaller orgs often use **Cloudflare, AWS, Azure** → you don't have permission to attack shared infra
- Find out if client needs **written approval from hosting provider** before you test
- Any host not explicitly in scope = **do not touch** even if you find it
- Document everything as you find it — screenshots, saved files, tool output

---

## Key Takeaways

- External recon is **passive** — no active scanning of real IPs unless explicitly authorized
- Breach data + email format = enough to start **internal password spraying**
- Job postings are underrated — reveal full tech stack and software versions
- Even a **low-privilege domain user account** is enough to start most internal AD enumeration
- `linkedin2username` + `Dehashed` combo = fastest path to a usable credential list