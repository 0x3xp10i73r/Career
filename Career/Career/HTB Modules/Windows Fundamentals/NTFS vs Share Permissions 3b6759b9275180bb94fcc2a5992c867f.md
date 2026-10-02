# NTFS vs Share Permissions

## Core Concept

**Two separate permission systems apply to shared resources:**

| Permission Type | Where It Applies | When It Applies |
| --- | --- | --- |
| **Share Permissions** | Network share ACL | Only when accessing via SMB over network |
| **NTFS Permissions** | File system level | Always — local AND network access |

> When both apply → **most restrictive wins**. Local/RDP access = only NTFS permissions matter.
> 

---

## Share Permissions (Simple — 3 levels)

| Permission | What it Allows |
| --- | --- |
| **Full Control** | Read + Change + change NTFS permissions |
| **Change** | Read, edit, delete, add files/subfolders |
| **Read** | View contents only |

---

## NTFS Permissions (Granular — 7 basic)

| Permission | What it Allows |
| --- | --- |
| Full Control | Add, edit, move, delete + change permissions |
| Modify | View + modify + delete |
| Read & Execute | Read + run programs |
| List Folder Contents | View listing of files/subfolders |
| Read | View contents only |
| Write | Add files, write changes |
| Special Permissions | Advanced options |

---

## Key Commands

### From Linux — SMB enumeration and connection

```bash
# List available shares
smbclient -L <SERVER_IP> -U htb-student

# Connect to a share
smbclient '\\<SERVER_IP>\Company Data' -U htb-student

# Mount share to local filesystem
sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //<IP>/"Company Data" /home/user/Desktop/

# Install cifs if needed
sudo apt-get install cifs-utils
```

### From Windows CMD

```bash
# View all shared folders on the system
net share
```

---

## Default Hidden Admin Shares (Always Present)

| Share | Points To | Purpose |
| --- | --- | --- |
| `C$` | `C:\` | Default share — entire C drive |
| `ADMIN$` | `C:\Windows` | Remote admin |
| `IPC$` | N/A | Remote IPC (inter-process communication) |

> **C$ is automatically shared on every Windows install** — anyone with proper credentials can access the entire C drive remotely via SMB
> 

---

## Windows Defender Firewall + SMB

Three firewall profiles:

- **Public** — most restrictive
- **Private** — moderate
- **Domain** — least restrictive (domain-joined systems)

> Non-domain systems block cross-workgroup SMB by default → enable specific inbound SMB rules rather than disabling firewall entirely
> 

**Authentication context:**

- Workgroup → authenticated against local **SAM database**
- Domain → authenticated against **Active Directory**

---

## Monitoring & Logging Tools

| Tool | What it Shows |
| --- | --- |
| **Computer Management** | Shares, Sessions, Open Files — who is connected and what they're accessing |
| **Event Viewer** | Logs of all system/security events — SMB access, logon events, file access |
| **net share** | Command-line view of all current shares |

**Event Viewer** = log viewer for Windows (answer to Question 2)

**Full path to Company Data share:**`C:\Users\htb-student\Desktop\Company Data` (answer to Question 3 — visible in `net share` output)

---

## Pentesting Relevance

| Scenario | Relevance |
| --- | --- |
| `Everyone` group on share with `Read` | Can enumerate files — look for credentials, configs |
| `Everyone` with `Full Control` | Can write files → potential payload staging |
| Default `C$` share | If credentials found → full remote filesystem access |
| Gray checkmarks in NTFS Security tab | Inherited permissions → trace back to parent |
| Event Viewer | **Blue team tool** — check for evidence of your activities during engagement |
| Computer Management → Open Files | See active connections and open files on shares |

---

## Key Takeaways

- Share permissions = **only for network SMB access**; NTFS = **always applies**
- The **C$ share** is created by default on every Windows install — massive attack surface
- `net share` = fast way to see what's shared on a system during post-exploitation
- **Event Viewer** logs everything — clean up or be aware your actions are logged
- Gray checkmarks in permissions = **inherited**, not explicitly set
- Most restrictive permission between Share and NTFS is what the user actually gets