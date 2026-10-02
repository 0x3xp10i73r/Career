# Active Directory

Status: Not started

## What is AD

Like a **phone book** for an organization — stores and manages all information about users, computers, and resources on a network.

---

## Physical Components

| Component | Description |
| --- | --- |
| **Domain Controller (DC)** | The "admin" of AD — manages everything, authenticates users, enforces policy |
| **NTDS.dit** | The crown jewel — database file containing **all AD data including password hashes** for every user |

> NTDS.dit = primary target in AD attacks. If you get this file → you own the domain (offline cracking of all hashes)
> 

---

## Logical Components

| Component | Description |
| --- | --- |
| **AD Schema** | Blueprint of AD — defines what objects can exist and what attributes they have. Format: `class:object` |
| **Domain** | Groups objects (users, computers, printers) together into a single organization unit |
| **Tree** | A group of domains sharing a common namespace |
| **Forest** | Collection of trees — the **top-level boundary** of AD |
| **Organizational Unit (OU)** | Containers inside a domain — used to organize users, groups, computers, apply Group Policy |
| **Trusts** | Define how domains/forests share access to resources |

---

## AD Hierarchy (Bottom → Top)

```
Object (user, computer, printer)
    ↓
OU (container grouping objects)
    ↓
Domain (groups OUs + objects)
    ↓
Tree (group of domains)
    ↓
Forest (top-level, collection of trees)
```

---

## Trust Types

| Trust Type | Direction | Description |
| --- | --- | --- |
| **Directional** | One-way | Domain A trusts Domain B — but B does NOT automatically trust A |
| **Transitive** | Two-way | If A trusts B and B trusts C → A automatically trusts C |

---