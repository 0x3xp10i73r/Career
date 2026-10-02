# Active Directory Research Over the Years

## Why This Matters

New critical AD vulnerabilities are discovered constantly. Staying current = essential for both attackers and defenders.

---

## Timeline (Most Recent First)

### 2021

| Attack/Tool | What it does |
| --- | --- |
| **PrintNightmare** (CVE-2021-34527) | RCE via Windows Print Spooler → take over AD hosts |
| **Shadow Credentials** | Low-priv user impersonates other users/computers → domain privesc |
| **noPac** (Dec 2021) | Standard domain user → **full domain control** (CVE-2021-42278/42287) |

### 2020

| Attack/Tool | What it does |
| --- | --- |
| **ZeroLogon** (CVE-2020-1472) | Impersonate any unpatched domain controller → domain takeover |

### 2019

| Attack/Tool | What it does |
| --- | --- |
| Kerberoasting Revisited (harmj0y) | New approaches to Kerberoasting attacks |
| **RBCD Abuse** (Elad Shamir) | Resource-based constrained delegation attack techniques |
| **Empire 3.0/4.0** (BC Security) | PowerShell Empire rewritten in Python3 |

### 2018

| Attack/Tool | What it does |
| --- | --- |
| **Printer Bug / SpoolSample** (Lee Christensen) | Coerce Windows hosts to authenticate via MS-RPRN → hash capture |
| **Rubeus** (harmj0y) | Full Kerberos abuse toolkit |
| **Forest Trust Attacks** (harmj0y) | Attacks across forest trust boundaries |
| **DCShadow** (LE TOUX + Delpy) | Rogue DC attack — inject AD data without detection |
| **PingCastle** (LE TOUX) | AD security auditing → risk scoring + misconfig report |

### 2017

| Attack/Tool | What it does |
| --- | --- |
| **ASREPRoasting** | Attack accounts without Kerberos preauthentication required |
| **ACE Up the Sleeve** (_wald0 + harmj0y, Black Hat/DEF CON) | AD ACL attacks — pivotal research |
| **Domain Trust Attack Guide** (harmj0y) | Enumerating + attacking domain trusts |

### 2016

| Attack/Tool | What it does |
| --- | --- |
| **BloodHound** (DEF CON 24) | **Game changer** — visual attack path mapping in AD |

### 2015

| Attack/Tool | What it does |
| --- | --- |
| **PowerShell Empire** | Post-exploitation framework |
| **PowerView 2.0** | AD reconnaissance via PowerShell |
| **DCSync** (Delpy + LE TOUX, via Mimikatz) | Simulate DC replication → dump all password hashes |
| **CrackMapExec v1.0** | AD enumeration + attack toolkit |
| **Kerberos Unconstrained Delegation** (Sean Metcalf, Black Hat USA) | Danger of unconstrained delegation exposed |
| **Impacket toolkit** | Python tools for AD attacks — **still actively maintained** |

### 2014

| Attack/Tool | What it does |
| --- | --- |
| **Veil-PowerView** → later **PowerView** | First AD recon via PowerShell |
| **Kerberoasting** (Tim Medin, SANS Hackfest 2014) | First public presentation of Kerberoasting |

### 2013

| Attack/Tool | What it does |
| --- | --- |
| **Responder** (Laurent Gaffie) | LLMNR/NBT-NS/MDNS poisoning → hash capture + SMB relay |

---

## Key Researchers to Follow

| Researcher | Key Contributions |
| --- | --- |
| **harmj0y** | Rubeus, PowerView, Kerberoasting, ASREPRoasting, ACL attacks, Domain Trusts |
| **Benjamin Delpy** | Mimikatz, DCSync, DCShadow |
| **Vincent LE TOUX** | DCShadow, PingCastle |
| **Sean Metcalf** | Kerberos attacks, AD security research |
| **Elad Shamir** | RBCD attacks, Shadow Credentials |
| **Laurent Gaffie** | Responder |
| **Lee Christensen** | Printer Bug, SpoolSample |
| **_wald0** | ACL attacks |

---

## Key Takeaway

> BloodHound (2016) and Impacket (2015) remain the most foundational tools in AD pentesting today. Kerberoasting (2014) and Responder (2013) are still among the most commonly used attack techniques over a decade later.
>