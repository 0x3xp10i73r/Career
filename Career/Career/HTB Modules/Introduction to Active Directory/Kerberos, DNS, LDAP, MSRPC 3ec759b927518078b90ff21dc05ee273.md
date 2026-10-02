# Kerberos, DNS, LDAP, MSRPC

[One More Note](Kerberos,%20DNS,%20LDAP,%20MSRPC/One%20More%20Note%203ec759b9275180359ee5ccb1f3a9c28d.md)

# Protocol Ports Quick Reference

| Protocol | Port | Transport |
| --- | --- | --- |
| **Kerberos** | **88** | TCP + UDP |
| **DNS** | **53** | UDP (default), TCP (fallback >512 bytes) |
| **LDAP** | **389** | TCP |
| **LDAPS** | **636** | TCP (SSL) |

---

## Kerberos Authentication

Default auth protocol for domain accounts since Windows 2000. **Ticket-based** — password never sent over network. **Stateless** — KDC doesn't track previous transactions.

### Flow (from Image 1)

```
1. KRB_AS_REQ  → Client sends auth request (password encrypts timestamp) → KDC
2. KRB_AS_REP  ← KDC validates, issues TGT (encrypted with krbtgt hash) ← KDC
3. KRB_TGS_REQ → Client presents TGT, requests TGS for specific service → KDC
4. KRB_TGS_REP ← KDC issues TGS (encrypted with SERVICE account's NTLM hash) ← KDC
5. KRB_AP_REQ  → Client presents TGS to service → service grants access (AP_REQ)
```

### Key Components

| Component | Role |
| --- | --- |
| **KDC** | Key Distribution Center — runs on DC, issues tickets |
| **TGT** | Ticket Granting Ticket — proves identity, used to request service tickets |
| **TGS** | Ticket Granting Service — service-specific ticket |
| **krbtgt** | Special account whose hash encrypts all TGTs — **Golden Ticket target** |
| **SPN** | Service Principal Name — identifies which service the TGS is for |

### Pentest Connection

- **Kerberoasting** = request TGS for SPN accounts → TGS encrypted with service account hash → crack offline
- **ASREPRoasting** = accounts with no pre-auth → get AS-REP without password → crack hash
- **Golden Ticket** = forge TGT using krbtgt hash → persist as any user
- **Pass-the-Ticket** = inject stolen TGT/TGS into session

---

## DNS

Translates hostnames → IP addresses. AD uses DNS for clients to locate DCs and SRV records.

### AD DNS Usage

- Clients query DNS to find DC via **SRV records**
- **Dynamic DNS** = auto-updates when IPs change
- LLMNR/NBT-NS = fallback when DNS fails (Responder exploits this)

### DNS Lookups

```powershell
# Forward lookup (domain → IP)
nslookup INLANEFREIGHT.LOCAL

# Reverse lookup (IP → hostname)
nslookup 172.16.6.5

# Find DC by hostname
nslookup ACADEMY-EA-DC01
```

**DNS flow (Image 2):**

```
1. Client requests inlanefreight.com → DNS Server
2. DNS returns IP: 134.209.24.248
3. Client makes HTTP request to 134.209.24.248
4. Server responds
```

---

## LDAP

Language applications use to communicate with AD. **AD is to LDAP as Apache is to HTTP.**

- Port **389** (plain), Port **636** (LDAPS/SSL)
- RFC 4511 = LDAP v3 specification
- LDAP messages sent **cleartext by default** → sniffable → use TLS

### LDAP Auth Types

| Type | Description |
| --- | --- |
| **Simple Authentication** | Username + password BIND request (includes anonymous auth) |
| **SASL** | Uses Kerberos or other services to bind → more secure |

**LDAP flow (Image 3):**

![image.png](Kerberos,%20DNS,%20LDAP,%20MSRPC/image.png)

```
Client App → API Gateway → LDAP Queries → Active Directory → returns user info
```

---

## MSRPC — Key Interfaces

| Interface | Used For | Attack Relevance |
| --- | --- | --- |
| **lsarpc** | LSA — local security policy, audit, interactive auth | Domain security policy management |
| **netlogon** | Continuous background auth service | ZeroLogon (CVE-2020-1472) abuses this |
| **samr** | SAM database — user/group/computer management | BloodHound uses this for domain recon. **Default: all auth users can query.** Restrict to admins only via registry key |
| **drsuapi** | DC replication (DRS Remote Protocol) | **DCSync attack** — simulate DC replication → dump NTDS.dit → all password hashes |

### Protecting samr

By default all authenticated users can query samr → change registry key to restrict to admins only to prevent BloodHound-style recon.

---

## Question Answers

| Question | Answer |
| --- | --- |
| Kerberos networking port | **88** |
| Protocol that translates names to IP addresses | **DNS** |
| RFC 4511 specifies what protocol | **LDAP** |

---

## Pentest Summary — Protocol Attacks

| Protocol | Attack |
| --- | --- |
| Kerberos | Kerberoasting, ASREPRoasting, Golden Ticket, Pass-the-Ticket |
| DNS | DNS zone transfer, LLMNR/NBT-NS poisoning (when DNS fails) |
| LDAP | LDAP enumeration (windapsearch, ldapsearch), cleartext sniffing |
| MSRPC (samr) | BloodHound recon |
| MSRPC (drsuapi) | DCSync → dump all hashes from NTDS.dit |