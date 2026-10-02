# Active Directory Structure

## AD Hierarchy (Top → Bottom)

```
Forest (security boundary)
    └── Domain (root)
        ├── Child Domain
        │   └── Sub-domain
        └── Organizational Units (OUs)
            ├── Users
            ├── Computers
            └── Groups
```

---

## Core Components

| Component | Description |
| --- | --- |
| **Forest** | Top-level security boundary — contains one or more domains. All objects under same admin control |
| **Domain** | Structure containing users, computers, groups. Has its own policies |
| **Child Domain** | Subdomain under a parent domain (e.g., `admin.inlanefreight.local`) |
| **OU (Organizational Unit)** | Container within a domain for organizing objects + applying GPOs |
| **Trust** | Relationship between domains/forests allowing cross-domain resource access |

---

## Example Structure (From Section)

```
INLANEFREIGHT.LOCAL/                    ← Root domain
├── ADMIN.INLANEFREIGHT.LOCAL           ← Child domain
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS → FILE01
│           ├── GROUPS → HQ Staff
│           └── USERS → barbara.jones
├── CORP.INLANEFREIGHT.LOCAL            ← Child domain
└── DEV.INLANEFREIGHT.LOCAL             ← Child domain
```

---

## Forest Trust — Key Concepts (From Image 2)

**Bidirectional trust** between `inlanefreight.local` ↔ `freightlogistics.local`:

- Users in Forest A can access resources in Forest B and vice versa

**Critical point:** Trust between root domains does NOT automatically extend to all child domains

```
inlanefreight.local ↔ freightlogistics.local  ✅ (bidirectional)

admin.dev.freightlogistics.local → wh.corp.inlanefreight.local  ❌ (no direct trust)
```

To allow child-to-child communication across forests = **separate trust must be explicitly created**

---

## What AD Stores (Enumerable by ANY Domain User)

| Category | Examples |
| --- | --- |
| Domain Computers | All joined machines |
| Domain Users | All user accounts |
| Domain Groups | Security/distribution groups |
| OUs | Organizational structure |
| Default Domain Policy | Password policy, lockout policy |
| Functional Domain Levels | Domain/forest functional level |
| GPOs | Group Policy Objects |
| Domain Trusts | Inter-domain/forest trust relationships |
| ACLs | Access Control Lists on objects |

---

## Question Answers

| Question | Answer |
| --- | --- |
| AD structure containing one or more domains | **Forest** |
| Multiple domains linked by trusts — True or False? | **True** |
| AD provides authentication and ____? | **authorization** |

---

## Key Takeaways

- **Forest = security boundary** — the most important concept for attack/defense scoping
- A basic domain user can enumerate **all of the above categories** — massive info exposure by design
- Domain trusts = common attack path — especially forest trusts that are misconfigured
- Child domain trusts with parent ≠ automatic trust with other child domains in another forest
- Understanding the structure is essential before attacking — "easier to break if you know how to build"