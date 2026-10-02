# Shells & Payloads - Intro

## Core Definitions

**Shell** = text-based interface for inputting commands and viewing output (Bash, Zsh, cmd, PowerShell)
**Payload** = code/data crafted to exploit a vulnerability and deliver the actual attack action

> Simple relationship: **Payloads deliver shells.** Exploiting a vulnerability (payload) is the *means*; gaining a shell is the *result*.
> 

---

## Why Shells Matter

| Benefit | Why it matters |
| --- | --- |
| Direct OS access | Run system commands, access file system |
| Enables further attacks | Privilege escalation, pivoting, file transfer |
| Persistence | More time to operate on target |
| Stealth | CLI shells are **harder to detect** than GUI access (RDP/VNC) |
| Speed | Faster navigation + easier to automate |
| Documentation | Easier to log/capture actions for reporting |

---

## Three Perspectives on "Shell"

| Perspective | Meaning |
| --- | --- |
| **Computing** | Standard userland CLI environment (Bash, cmd, PowerShell) |
| **Exploitation/Security** | Result of exploiting a vuln to gain interactive host access (e.g., EternalBlue → cmd prompt) |
| **Web** | A script (often via file upload vuln) that lets attacker run commands/read files through a browser interface |

---

## Payload Definitions by Context

| Field | Definition |
| --- | --- |
| Networking | The actual data inside a packet (excluding headers) |
| Basic Computing | The "action" portion of an instruction set |
| Programming | Data carried by a language instruction |
| **Exploitation/Security** (relevant here) | Code crafted to exploit a vulnerability — includes malware, ransomware, etc. |

---

## Key Takeaways

- **Shell vs Payload distinction:** payload = delivery mechanism/exploit code, shell = the resulting access
- CLI shells > GUI access for stealth, speed, and automation in offensive engagements
- This module's focus = **post-exploitation entry point** — what happens right after a vulnerability is successfully triggered
- Web shells are a distinct category — operated via browser, not a traditional terminal connection