# One More Note

This section is **Kerberos, DNS, LDAP, and MSRPC**. I’ve converted it into the same Obsidian-style format, keeping the HTB terminology and the pentesting-relevant points.

# Kerberos, DNS, LDAP & MSRPC

> 
> 
> 
> Active Directory relies on several core protocols for authentication, directory access, name resolution, and remote communication:
> 
> - **Kerberos** → Authentication
> - **DNS** → Name resolution and locating AD services
> - **LDAP** → Directory queries and communication with AD
> - **MSRPC** → Remote procedure calls and AD management

---

# 1. Kerberos

## What is Kerberos?

**Kerberos** is the default authentication protocol for domain accounts since **Windows 2000**.

It is:

- An open standard
- Ticket-based
- Designed for mutual authentication
- Used extensively by Active Directory
- Based on a **Key Distribution Center (KDC)**

> [!important]
> 
> 
> Kerberos allows authentication without transmitting the user's password across the network.
> 

---

## Kerberos Components

```
Client
  │
  │ AS-REQ
  ▼
KDC
  │
  │ TGT
  ▼
Client
  │
  │ TGS-REQ
  ▼
KDC
  │
  │ TGS
  ▼
Client
  │
  │ AP-REQ
  ▼
Service
```

### Key Terms

| Term | Meaning |
| --- | --- |
| **KDC** | Key Distribution Center |
| **TGT** | Ticket Granting Ticket |
| **TGS** | Ticket Granting Service ticket |
| **AS-REQ** | Authentication Service Request |
| **TGS-REQ** | Ticket Granting Service Request |
| **TGS-REP** | Ticket Granting Service Response |
| **AP-REQ** | Application Request |

---

# 2. Kerberos Authentication Process

### Step 1 — User Authentication

When a user logs in, the user's password is used to encrypt a timestamp.

The authentication request is sent to the **KDC**.

```
User
 │
 │ AS-REQ
 │
 ▼
KDC
```

The KDC attempts to decrypt and validate the request.

---

### Step 2 — TGT Issued

If authentication succeeds, the KDC creates a:

```
TGT = Ticket Granting Ticket
```

The TGT is encrypted using the secret key of the:

```
krbtgt
```

account.

```
KDC
 │
 │ TGT
 ▼
User
```

The TGT can then be used to request service tickets.

---

### Step 3 — Request TGS

The user presents the TGT to the Domain Controller and requests access to a specific service.

This is:

```
TGS-REQ
```

```
User
 │
 │ TGS-REQ + TGT
 ▼
KDC
```

If the TGT is valid, the KDC creates a service ticket.

---

### Step 4 — TGS Issued

The KDC creates the:

```
TGS = Ticket Granting Service ticket
```

The TGS is encrypted using the NTLM password hash of the service/computer account running the service.

```
KDC
 │
 │ TGS-REP
 ▼
User
```

---

### Step 5 — Access Service

The user presents the TGS to the requested service.

```
User
 │
 │ AP-REQ + TGS
 ▼
Service
```

If the ticket is valid:

```
Access Granted
```

---

## Complete Kerberos Flow

```
                  ┌──────────────┐
                  │     KDC      │
                  │ Domain Ctrl. │
                  └──────┬───────┘
                         │
           ┌─────────────┴─────────────┐
           │                           │
       AS-REQ                       TGS-REQ
           │                           │
           ▼                           ▼
        TGT issued                  TGS issued
           │                           │
           └───────────┬───────────────┘
                       │
                       ▼
                    Client
                       │
                    AP-REQ
                       │
                       ▼
                    Service
                       │
                       ▼
                 Access Granted
```

> [!important]
> 
> 
> The major security benefit is that the user's password does not need to be repeatedly transmitted to access network resources.
> 

---

## Kerberos Port

Kerberos uses:

```
Port 88
```

Both:

```
TCP/88
UDP/88
```

are used.

### Pentesting Relevance

When enumerating an AD environment, an open port **88** can help identify a Domain Controller.

Example:

```bash
nmap -p 88 <IP>
```

> [!tip]
> 
> 
> **Port 88 → Kerberos → Domain Controller**
> 

---

# 3. DNS

## What is DNS?

**DNS (Domain Name System)** translates hostnames into IP addresses.

Active Directory relies heavily on DNS.

DNS allows:

- Workstations to locate Domain Controllers
- Domain Controllers to communicate
- Clients to locate network services
- Hostnames to resolve to IP addresses

---

## DNS in Active Directory

AD maintains service information using:

```
SRV Records
```

SRV records allow clients to locate services such as:

- Domain Controllers
- File servers
- Printers
- Other network services

---

## Dynamic DNS

Active Directory uses **Dynamic DNS** to automatically update DNS records when systems change IP addresses.

Without Dynamic DNS, administrators would need to manually update records.

---

# 4. DNS Ports

DNS uses:

```
TCP/53
UDP/53
```

Normally:

```
UDP/53
```

is used.

DNS can fall back to:

```
TCP/53
```

when necessary, including situations where DNS messages are too large for the normal UDP exchange.

---

# 5. DNS and Domain Controller Discovery

When a client joins or communicates with an AD network, it can use DNS to locate a Domain Controller.

High-level process:

```
Client
  │
  │ DNS Query
  ▼
DNS Server
  │
  │ SRV Record
  ▼
Domain Controller Hostname
  │
  │ DNS Resolution
  ▼
Domain Controller IP
  │
  ▼
Client Communication
```

---

# 6. DNS Enumeration

## Forward DNS Lookup

A forward lookup resolves:

```
Hostname → IP Address
```

Example:

```powershell
nslookup INLANEFREIGHT.LOCAL
```

Example result:

```
Server:  172.16.6.5
Address: 172.16.6.5

Name:    INLANEFREIGHT.LOCAL
Address: 172.16.6.5
```

---

## Reverse DNS Lookup

A reverse lookup resolves:

```
IP Address → Hostname
```

Example:

```powershell
nslookup 172.16.6.5
```

Example:

```
Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address: 172.16.6.5
```

---

## Find IP Address of a Host

You can also resolve a hostname to an IP:

```powershell
nslookup ACADEMY-EA-DC01
```

Result:

```
Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address: 172.16.6.5
```

---

# 7. DNS Quick Reference

| Task | Direction | Command |
| --- | --- | --- |
| Forward lookup | Name → IP | `nslookup domain.local` |
| Reverse lookup | IP → Name | `nslookup 10.10.10.10` |
| Resolve host | Hostname → IP | `nslookup HOSTNAME` |

> [!tip]
> 
> 
> **DNS = Name Resolution + AD Service Discovery**
> 

---

# 8. LDAP

## What is LDAP?

**LDAP — Lightweight Directory Access Protocol** is used by Active Directory for directory lookups and communication.

LDAP is:

- Open-source
- Cross-platform
- Used by directory services
- Supported by Active Directory
- Defined by RFC 4511 for LDAPv3

LDAP allows systems and applications to communicate with directory services.

A useful mental model:

```
Application
     │
     │ LDAP
     ▼
Active Directory
```

---

## LDAP Ports

| Protocol | Port |
| --- | --- |
| LDAP | `389` |
| LDAPS | `636` |

```
LDAP  → TCP/389
LDAPS → TCP/636
```

---

# 9. LDAP and Active Directory

LDAP can be thought of as the communication language used by applications to interact with AD.

A useful analogy from the material:

```
Apache
  ↓
HTTP
```

is similar to:

```
Active Directory
  ↓
LDAP
```

AD is the directory service, while LDAP is the protocol used to communicate with it.

---

# 10. LDAP Session

An LDAP session begins by connecting to an LDAP server.

The server is also referred to as a:

```
Directory System Agent (DSA)
```

In an AD environment, the Domain Controller listens for LDAP requests.

High-level flow:

```
Client
  │
  │ LDAP Request
  ▼
Domain Controller
  │
  ▼
Active Directory
```

---

# 11. LDAP Authentication

LDAP uses a:

```
BIND
```

operation to establish the authentication state of an LDAP session.

There are two authentication methods discussed in the material.

---

## Simple Authentication

Simple authentication can include:

- Anonymous authentication
- Unauthenticated authentication
- Username/password authentication

For username/password authentication:

```
Username
+
Password
    ↓
BIND Request
    ↓
LDAP Server
```

---

## SASL Authentication

**SASL** stands for:

> Simple Authentication and Security Layer
> 

SASL can use another authentication service, such as:

```
Kerberos
```

to authenticate to LDAP.

High-level flow:

```
Client
  │
  │ Kerberos
  ▼
Authentication Service
  │
  │ SASL
  ▼
LDAP
```

SASL separates the authentication mechanism from the application protocol.

---

# 12. LDAP Security Consideration

The material notes that LDAP authentication messages are sent in cleartext by default.

This creates a risk of network sniffing.

> [!warning]
> 
> 
> LDAP should use TLS encryption or a similar mechanism to protect authentication information in transit.
> 

Comparison:

```
LDAP
Port 389
Potentially cleartext

LDAPS
Port 636
TLS-protected
```

---

# 13. MSRPC

## What is MSRPC?

**MSRPC** stands for:

> Microsoft Remote Procedure Call
> 

It is Microsoft's implementation of RPC.

RPC allows client-server applications to communicate and execute functionality remotely.

Windows uses MSRPC to access systems and services within Active Directory.

---

# 14. Important MSRPC Interfaces

The material identifies four important RPC interfaces:

| Interface | Purpose |
| --- | --- |
| `lsarpc` | Local Security Authority operations |
| `netlogon` | Domain authentication |
| `samr` | Remote SAM/account management |
| `drsuapi` | Directory replication operations |

---

# 15. lsarpc

`lsarpc` provides RPC calls to the:

```
Local Security Authority (LSA)
```

LSA manages:

- Local security policy
- Audit policy
- Interactive authentication services

LSARPC is also used for management of domain security policies.

```
Client
  │
  │ LSARPC
  ▼
LSA
  │
  ├── Security Policy
  ├── Audit Policy
  └── Authentication
```

---

# 16. netlogon

**Netlogon** is a Windows process/service used to authenticate:

- Users
- Services
- Other entities

within the domain environment.

It continuously runs in the background.

```
Domain Authentication
        │
        ▼
    Netlogon
```

---

# 17. samr

**SAMR** stands for the Remote Security Account Manager interface.

It provides management functionality for the domain account database.

It can be used to manage:

- Users
- Groups
- Computers

Operations include:

```
Create
Read
Update
Delete
```

---

## SAMR — Pentesting Relevance

SAMR can be used for internal domain reconnaissance.

The material specifically mentions tools such as:

```
BloodHound
```

for visually mapping AD relationships and attack paths.

### Enumeration Risk

The material notes that, by default, authenticated domain users can perform remote SAM queries and gather considerable information about the AD domain.

Organizations can restrict this behavior so that only administrators can perform remote SAM queries.

> [!tip] Pentesting Takeaway
> 
> 
> During an AD assessment, understand whether authenticated users can remotely query SAM information. This can significantly affect the amount of domain reconnaissance possible.
> 

---

# 18. drsuapi

`drsuapi` implements Microsoft's:

> Directory Replication Service Remote Protocol
> 

It is used for replication-related operations between Domain Controllers.

```
Domain Controller A
        │
        │ DRS / drsuapi
        ▼
Domain Controller B
```

---

## drsuapi — Security Relevance

The material notes that attackers can abuse replication functionality to obtain a copy of the Active Directory database (`NTDS.dit`) and retrieve password hashes.

Those hashes can potentially be:

- Used for Pass-the-Hash attacks
- Cracked offline with Hashcat
- Used to access systems through remote management protocols such as:
    - RDP
    - WinRM

> [!important]
> 
> 
> `drsuapi` is therefore highly relevant when studying **AD replication abuse and credential extraction**.
> 

---

# 19. Protocol Comparison

| Protocol | Purpose | Port |
| --- | --- | --- |
| **Kerberos** | Authentication | `88 TCP/UDP` |
| **DNS** | Name resolution / service discovery | `53 TCP/UDP` |
| **LDAP** | Directory communication | `389` |
| **LDAPS** | LDAP over SSL/TLS | `636` |
| **MSRPC** | Remote procedure calls | Varies |

---

# 20. AD Protocol Relationships

```
                    Active Directory
                           │
          ┌────────────────┼────────────────┐
          │                │                │
       Kerberos           DNS              LDAP
          │                │                │
   Authentication     Name Resolution   Directory Access
          │                │                │
          └────────────────┼────────────────┘
                           │
                         MSRPC
                           │
              Remote AD Management
```

---

# 21. Pentesting Enumeration Cheat Sheet

## Kerberos

Check:

```
TCP/88
UDP/88
```

Command:

```bash
nmap -p 88 <IP>
```

Purpose:

```
Potential Domain Controller identification
```

---

## DNS

Check:

```
TCP/53
UDP/53
```

Useful command:

```bash
nslookup <domain>
```

Reverse lookup:

```bash
nslookup <IP>
```

Look for:

```
SRV records
Domain Controllers
Hostnames
Internal DNS information
```

---

## LDAP

Check:

```
389
636
```

Remember:

```
389 → LDAP
636 → LDAPS
```

Authentication:

```
BIND
```

Methods:

```
Simple Authentication
SASL Authentication
```

---

## MSRPC

Important interfaces:

```
lsarpc
netlogon
samr
drsuapi
```

Pay particular attention to:

```
samr
↓
Domain reconnaissance

drsuapi
↓
Directory replication
↓
NTDS.dit / credential extraction
```

---

# 22. Exam Memory Sheet

> [!important] Memorize These
> 

### Ports

```
88  → Kerberos
53  → DNS
389 → LDAP
636 → LDAPS
```

### Kerberos

```
AS-REQ
   ↓
TGT
   ↓
TGS-REQ
   ↓
TGS
   ↓
AP-REQ
   ↓
Service Access
```

### LDAP

```
LDAP
├── 389
└── BIND
    ├── Simple Authentication
    └── SASL
```

### MSRPC

```
MSRPC
├── lsarpc
├── netlogon
├── samr
└── drsuapi
```

### Key Associations

```
Kerberos → Authentication

DNS → Name Resolution

LDAP → Directory Access

SAMR → Account Enumeration/Management

DRSUAPI → AD Replication
```

---

# 23. HTB Questions

## Question 1

**What networking port does Kerberos use?**

```
88
```

---

## Question 2

**What protocol is utilized to translate names into IP addresses?**

```
DNS
```

---

## Question 3

**What protocol does RFC 4511 specify?**

```
LDAP
```

---

# 24. One-Line Definitions

> **Kerberos** → Ticket-based authentication protocol used by AD.
> 

> **KDC** → Kerberos service on a Domain Controller responsible for issuing tickets.
> 

> **TGT** → Ticket used to request service tickets.
> 

> **TGS** → Ticket used to access a specific service.
> 

> **DNS** → Resolves names to IP addresses and helps AD clients locate services.
> 

> **SRV Record** → DNS record used to locate network services.
> 

> **LDAP** → Protocol used to communicate with directory services such as AD.
> 

> **BIND** → LDAP operation used to establish an authenticated LDAP session.
> 

> **SASL** → Framework allowing authentication mechanisms such as Kerberos to be used with LDAP.
> 

> **MSRPC** → Microsoft's implementation of Remote Procedure Call.
> 

> **LSARPC** → RPC interface for Local Security Authority functions.
> 

> **Netlogon** → Windows service involved in domain authentication.
> 

> **SAMR** → RPC interface for managing/enumerating users, groups, and computers.
> 

> **DRSUAPI** → RPC interface implementing AD replication operations.
> 

---

# 25. Mental Model

```
                     CLIENT
                        │
             ┌──────────┼──────────┐
             │          │          │
             ▼          ▼          ▼
           DNS       Kerberos     LDAP
             │          │          │
             │      Authenticate    │
             │          │       Query AD
             │          │          │
             └──────────┼──────────┘
                        │
                        ▼
               DOMAIN CONTROLLER
                        │
                        │ MSRPC
                        ▼
              AD Management / RPC
                 │      │      │
                 ▼      ▼      ▼
              LSARPC  SAMR  DRSUAPI
```

> [!tip] Easy Memory Trick
> 
> 
> **DNS finds it → Kerberos authenticates you → LDAP talks to AD → MSRPC performs Windows/AD remote operations.**
> 

The source section identifies this as **Section 7/16** of the HTB *Introduction to Active Directory* module and includes the three questions above.