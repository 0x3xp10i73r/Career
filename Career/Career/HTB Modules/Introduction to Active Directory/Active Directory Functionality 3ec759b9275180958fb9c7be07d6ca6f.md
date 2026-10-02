# Active Directory Functionality

## FSMO Roles (5 Total)

| Role | Scope | Key Responsibility |
| --- | --- | --- |
| **Schema Master** | Per forest (1) | Manages read/write copy of AD schema |
| **Domain Naming Master** | Per forest (1) | Ensures no duplicate domain names in forest |
| **RID Master** | Per domain (1) | Assigns RID blocks to DCs → prevents duplicate SIDs |
| **PDC Emulator** | Per domain (1) | Auth requests, password changes, GPO management, **time sync** |
| **Infrastructure Master** | Per domain (1) | Translates GUIDs/SIDs/DNs between domains. If  broken → ACLs show SIDs not names |

---

## Domain Functional Levels — Key Features

| Level | Key Addition |
| --- | --- |
| Windows 2000 native | Universal groups, group nesting, SID history |
| Windows Server 2003 | `lastLogonTimestamp`, constrained delegation, selective auth |
| Windows Server 2008 | AES 128/256 for Kerberos, **fine-grained password policies**, DFS replication |
| **Windows Server 2008 R2** | **Managed Service Accounts**, Authentication mechanism assurance |
| Windows Server 2012 | KDC claims, compound auth, Kerberos armoring |
| Windows Server 2012 R2 | Protected Users group protections, Auth Policies/Silos |
| Windows Server 2016 | Smart card required for interactive logon, new Kerberos + credential protections |

---

## Forest Functional Levels — Key Features

| Version | Key Addition |
| --- | --- |
| Server 2003 | **Forest trusts**, domain renaming, RODCs |
| Server 2008 R2 | **AD Recycle Bin** |
| Server 2016 | Privileged Access Management (PAM) via MIM |

---

## Trust Types (From Image)

| Trust Type | Direction | Transitive? | Description |
| --- | --- | --- | --- |
| **Parent-child** | Two-way | ✅ Yes | Child domain ↔ parent domain within same forest |
| **Cross-link** | Two-way | ✅ Yes | Between **child domains** to speed up auth (skip going up to root) |
| **External** | One or Two-way | ❌ No | Between domains in **separate forests** — uses SID filtering |
| **Tree-root** | Two-way | ✅ Yes | Between forest root and new tree root domain |
| **Forest** | One or Two-way | ✅ Yes | Between **two forest root domains** |

### From the diagram:

![image.png](Active%20Directory%20Functionality/image.png)

- `inlanefreight` → `freightlogistics` = **Tree-root** (purple, one-way)
- `inlanefreight` → `corp.inlanefreight` = **Parent-child** (green dashed, two-way)
- `corp.inlanefreight` → `wh.corp.inlanefreight` = **Parent-child** (green dashed, two-way)
- `wh.corp.inlanefreight` ↔ `admin.dev.inlanefreight` = **Cross-link** (blue, two-way)
- `dev.inlanefreight` → `shippinglanes` (external forest) = **External** (teal dotted)

---

## Transitive vs Non-Transitive

```
Transitive:    A trusts B, B trusts C → A automatically trusts C
Non-transitive: A trusts B, B trusts C → A does NOT trust C
```

---

## Trust Security Risks

- Misconfigured trusts = **attack path between domains/forests**
- Bidirectional trusts from M&A = unintended risk to acquiring company
- Can Kerberoast across a forest trust → get admin account in principal domain
- **One-way trust direction ≠ access direction** (trust direction is opposite to access direction)

---

## Question Answers

| Question | Answer |
| --- | --- |
| Role that maintains time for a domain | **PDC Emulator** |
| Functional level that introduced Managed Service Accounts | **Windows Server 2008 R2** |
| Trust type between two child domains | **Cross-link** |
| Role ensuring no duplicate SIDs | **Relative ID (RID) Master** |