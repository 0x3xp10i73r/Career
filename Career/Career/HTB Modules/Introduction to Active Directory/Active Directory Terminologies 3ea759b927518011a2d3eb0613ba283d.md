# Active Directory Terminologies

[One more note](Active%20Directory%20Terminologies/One%20more%20note%203ec759b92751807e925eeb5a16d0b904.md)

## Core Object Types

| Term | Definition |
| --- | --- |
| **Object** | ANY resource in AD — users, OUs, printers, DCs, computers, etc. |
| **Attributes** | Characteristics of an object (e.g., hostname, displayName). Each has an LDAP name |
| **Schema** | Blueprint of AD — defines what object types can exist and their attributes. Objects are *instances* of classes |
| **Container** | Holds other objects; has a defined place in the hierarchy |
| **Leaf** | Does NOT contain other objects; found at end of hierarchy |

---

## Naming & Identity

| Term | Definition | Example |
| --- | --- | --- |
| **GUID** | 128-bit unique identifier assigned to every AD object. Stored in `ObjectGUID`. Never changes | `{abc123...}` |
| **SID** | Security Identifier — unique ID for security principals and groups. Used in access tokens | `S-1-5-21-...` |
| **DN (Distinguished Name)** | Full path to an object in AD | `cn=bjones,ou=IT,ou=Employees,dc=inlanefreight,dc=local` |
| **RDN (Relative DN)** | Single unique component within its parent container | `bjones` |
| **sAMAccountName** | User logon name — max 20 chars, must be unique | `bjones` |
| **userPrincipalName** | Alternative identifier in email format (not mandatory) | `bjones@inlanefreight.local` |
| **FQDN** | Full computer name with domain | `DC01.INLANEFREIGHT.LOCAL` |
| **SPN** | Uniquely identifies a service instance — used by Kerberos auth | `MSSQLSvc/server.domain.local:1433` |

**From the image:** `inlanefreight.local/Users/Sales/Managers/BJones`

- **DN** = entire path (unique in directory)
- **RDN** = `BJones` (unique within its OU)

---

## FSMO Roles (5 Total)

| Role | Scope | Purpose |
| --- | --- | --- |
| **Schema Master** | Per forest (1) | Controls schema changes |
| **Domain Naming Master** | Per forest (1) | Controls adding/removing domains |
| **RID Master** | Per domain (1) | Assigns RID pools to DCs for new SIDs |
| **PDC Emulator** | Per domain (1) | Auth, password changes, time sync, GPO updates |
| **Infrastructure Master** | Per domain (1) | Handles cross-domain object references |

> All 5 assigned to first DC in forest root. New domains added → only RID, PDC, Infrastructure assigned
> 

---

## Key AD Components

| Component | Key Details |
| --- | --- |
| **Global Catalog (GC)** | DC that stores full copy of own domain + partial copy of all other domains in forest. Enables forest-wide search |
| **RODC** | Read-only DC — no cached passwords (except its own), no changes pushed out, used in branch offices |
| **Replication** | AD changes synced between DCs via KCC (Knowledge Consistency Checker) connections |
| **GPO** | Virtual policy collection with unique GUID. Applied to users/computers at domain or OU level |
| **SYSVOL** | Shared folder on DCs storing GPOs, logon scripts, policies. Replicated via FRS |
| **AdminSDHolder** | Manages ACLs for privileged group members. SDProp runs every hour to restore correct ACLs |
| **AD Recycle Bin** | Introduced Server 2008 R2. Preserves deleted objects with attributes for 60 days (default) |
| **Tombstone** | Deleted object container. Objects stripped of most attributes. Default lifetime: 60 or 180 days |

---

## Access Control

| Term | Definition |
| --- | --- |
| **ACL** | Ordered collection of ACEs applied to an object |
| **ACE** | Identifies a trustee + defines allowed/denied/audited access rights |
| **DACL** | Controls who has access. No DACL = full access to everyone. Empty DACL = deny all |
| **SACL** | Logs access attempts to secured objects (audit trail) |

---

## Attack-Relevant Terms

| Term | Pentest Relevance |
| --- | --- |
| **NTDS.DIT** | Heart of AD — stored at `C:\Windows\NTDS\`. Contains all user password hashes. **Primary target for domain compromise** |
| **sIDHistory** | Can be abused to gain elevated access from previous domain migrations if SID Filtering disabled |
| **adminCount=1** | Marks accounts protected by SDProp. Attackers target these — likely privileged accounts |
| **SPN** | Used in **Kerberoasting** — request TGS ticket for SPN-associated accounts → crack offline |
| **GUID** | Most reliable way to query specific AD objects — never changes |
| **dsHeuristics** | Can exclude groups from AdminSDHolder protection — misconfiguration risk |

---

## Question Answers

| Question | Answer |
| --- | --- |
| "Blueprint" of AD environment | **Schema** |
| What uniquely identifies a service instance? | **Service Principal Name** |
| GPOs applied to user and computer objects? | **True** |
| Container holding deleted AD objects | **Tombstone** |
| File containing all user password hashes | **NTDS.DIT** |