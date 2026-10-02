# Why Active Directory?

# Introduction to Active Directory

## Why AD Matters

- **95% of Fortune 500 companies** run Active Directory
- Microsoft has near-complete monopoly in directory services space
- On-prem AD is not going away despite cloud migration trends
- Nearly every network pentest will involve AD in some form

---

## Key Security Facts

- AD is essentially a **large read-only database accessible to ALL domain users** regardless of privilege
- A **standard domain user** (no special privileges) can enumerate most AD objects
- Multiple attacks possible with just a basic domain user account
- Designed for backward compatibility → **not secure by default**
- New critical vulnerabilities discovered regularly

**Recent notable AD attacks:**

| Attack | CVE | Impact |
| --- | --- | --- |
| PrintNightmare | CVE-2021-34527 | Privesc → SYSTEM |
| Zerologon | CVE-2020-1472 | Domain takeover |
| noPac | CVE-2021-42278/42287 | DA from standard user |

> Conti ransomware used PrintNightmare + Zerologon in 400+ real-world attacks
> 

---

## AD Timeline / History

| Year | Milestone |
| --- | --- |
| 1971 | LDAP foundations in RFCs |
| 1990 | Windows NT 3.0 — first Microsoft directory attempt |
| 1993 | Novell Directory Services (X.500 concept predecessor) |
| 1997 | First Active Directory beta |
| **2000** | **AD released with Windows Server 2000** |
| 2003 | Forest feature added (containers for separate domains) |
| 2008 | **ADFS** (Active Directory Federation Services) — SSO across apps |
| 2016 | **gMSA** (Group Managed Service Accounts), Azure AD Connect, cloud migration tools |

---

## What AD Manages

- Users and Groups
- Computers
- Network devices
- File shares
- Group Policies (GPOs)
- Trusts between domains/forests
- Authentication and Authorization

---

## Core AD Functions

- **Authentication** — verifies who you are (Kerberos/NTLM)
- **Authorization** — determines what you can access
- **Centralized Management** — one place to manage all resources

---

## Why Pentesters Need Deep AD Knowledge

- Tools are only as good as the operator understands them
- Need to find **both obvious and subtle misconfigurations**
- Must provide actionable remediation advice to clients
- Understanding the "why" behind attacks = better exploitation + better reporting

---

## Key Takeaway

> A standard domain user account = enough to enumerate the entire domain and find attack paths. This is why AD security is so critical and so commonly broken.
>