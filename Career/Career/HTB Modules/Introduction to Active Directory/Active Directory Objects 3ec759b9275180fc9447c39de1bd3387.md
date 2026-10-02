# Active Directory Objects

## Object Types: Security Principal vs Not

| Object | Leaf/Container | Security Principal | Has SID | Has GUID |
| --- | --- | --- | --- | --- |
| **User** | Leaf | ✅ Yes | ✅ | ✅ |
| **Computer** | Leaf | ✅ Yes | ✅ | ✅ |
| **Group** | Container | ✅ Yes | ✅ | ✅ |
| **Contact** | Leaf | ❌ No | ❌ | ✅ |
| **Printer** | Leaf | ❌ No | ❌ | ✅ |
| **Shared Folder** | Leaf | ❌ No | ❌ | ✅ |
| **OU** | Container | ❌ No | ❌ | ✅ |

> **Security Principal** = can be authenticated by the OS + can manage access to resources
> 

---

## Object Details

### Users

- Leaf objects, security principals (SID + GUID)
- 800+ possible attributes (display name, last login, password change, email, manager, etc.)
- **Primary attack target** — even low-priv user = full domain enumeration capability

### Computers

- Leaf objects, security principals (SID + GUID)
- `NT AUTHORITY\SYSTEM` on a domain-joined computer ≈ standard domain user
- **High-value target** — SYSTEM access = can enumerate domain like a user

### Groups

- **Container** objects (hold users, computers, other groups)
- Security principals (SID + GUID)
- **Nested groups** = common source of unintended privilege escalation
- BloodHound = best tool for visualizing nested group attack paths

### OUs (Organizational Units)

- Container objects for organizing similar objects
- Used for **administrative delegation** without full admin rights
- GPOs applied at OU level → policy scoping
- Example: Help Desk OU → reset passwords for all users in that OU only

### Domain Controllers

- "Brain" of AD network
- Handle **all authentication requests**
- Enforce security policies
- Store information about every object in the domain

### Foreign Security Principals (FSP)

- Created automatically when external forest user/group added to a local group
- Placeholder holding the **SID of the foreign object**
- Stored in: `cn=ForeignSecurityPrincipals,dc=inlanefreight,dc=local`
- Used to resolve object names via trust relationships

---

## Pentest Attack Angles by Object Type

| Object | Attack Relevance |
| --- | --- |
| **Users** | Password spraying, Kerberoasting, ASREPRoasting target |
| **Computers** | Gain SYSTEM → enumerate like domain user |
| **Groups** | Nested group membership → unintended privesc |
| **Shared Folders** | May be open to all authenticated users including computer accounts |
| **OUs** | Misconfigured delegation → user can modify objects in OU |
| **FSP** | SID history abuse, cross-forest attacks |

---

## Nested Group Membership — Key Concept

```
User A → Group B → Group C → Domain Admins
```

User A **inherits** Domain Admin rights through nested membership even though they're not directly in Domain Admins. BloodHound visualizes these paths automatically.

---

## Question Answers

| Question | Answer |
| --- | --- |
| Computers are leaf objects — True or False? | **True** |
| Objects used to store similar objects for ease of admin | **Organizational Units (OUs)** |
| AD object that handles all authentication requests | **Domain Controller** |