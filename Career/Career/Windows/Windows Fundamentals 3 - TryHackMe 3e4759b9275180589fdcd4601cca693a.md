# Windows Fundamentals 3 - TryHackMe

## Windows Update

- Released on **Patch Tuesday** (2nd Tuesday of each month)
- Critical updates can be pushed outside of Patch Tuesday
- Windows 10+ = updates **cannot be permanently ignored**, only postponed
- Managed via: **Settings → Windows Update**
- CMD shortcut: `control /name Microsoft.WindowsUpdate`

---

## Windows Security — Status Icons

| Color | Meaning |
| --- | --- |
| 🟢 Green | Protected — no action needed |
| 🟡 Yellow | Safety recommendation to review |
| 🔴 Red | Immediate attention required |

---

## Protection Areas

### Virus & Threat Protection

**Scan types:**

- **Quick scan** — common threat locations
- **Full scan** — all files + running programs (1+ hour)
- **Custom scan** — user-selected locations

**Key settings:**

| Setting | Purpose |
| --- | --- |
| Real-time protection | Stops malware installing/running |
| Cloud-delivered protection | Faster protection via cloud data |
| Automatic sample submission | Sends samples to Microsoft |
| Controlled folder access | Blocks unauthorized changes (ransomware protection) |
| Exclusions | Files/folders skipped by AV scanner |

> ⚠️ Exclusions = attack opportunity — malware/pentesters drop payloads in excluded folders
> 

---

### Firewall & Network Protection

**Three Profiles:**

| Profile | When used |
| --- | --- |
| **Domain** | Connected to domain controller (corporate network) |
| **Private** | Home/trusted private network |
| **Public** | Public Wi-Fi (airports, coffee shops) — most restrictive |

> Airport Wi-Fi = **Public** profile
> 

**CMD shortcut:** `WF.msc`

---

### App & Browser Control (SmartScreen)

**SmartScreen settings:** Warn / Block / Off

- Checks unrecognized apps/files from the web
- Protects against phishing, malware websites

**Exploit protection:** Built into Windows 10/Server 2019 — leave at defaults unless you know what you're doing

---

### Device Security

**Core Isolation:**

- **Memory Integrity** — prevents malicious code injection into high-security processes

**TPM (Trusted Platform Module):**

- Hardware-based security chip
- Secure crypto-processor
- Tamper-resistant
- Works with BitLocker for full disk encryption

---

## BitLocker

- Full disk encryption feature
- Best protection when combined with **TPM 1.2 or later**
- On systems **without TPM** → uses a **removable drive (USB) containing a startup key**

> Without TPM = startup key must be stored on removable drive
> 

---

## Volume Shadow Copy Service (VSS)

- Creates **point-in-time snapshots** of data for backup/recovery
- Stored in: `System Volume Information` folder on each protected drive
- Used for: restore points, system restore, recovery

**VSS tasks (when enabled):**

- Create restore point
- Perform system restore
- Configure restore settings
- Delete restore points

> **Ransomware** commonly deletes VSS shadow copies to prevent recovery — always maintain **offline/off-site backups**
> 

---

## Quick Reference — Security Tools

| Tool | Command | Purpose |
| --- | --- | --- |
| Windows Update | `control /name Microsoft.WindowsUpdate` | Check/install updates |
| Windows Firewall | `WF.msc` | Advanced firewall config |
| Windows Security | Start Menu → Windows Security | All-in-one security dashboard |
| On-demand scan | Right-click → Scan with Microsoft Defender | Scan specific file/folder |

---

## Pentesting Relevance

| Feature | Attack/Defense Relevance |
| --- | --- |
| AV Exclusions | Drop payloads in excluded folders to evade detection |
| Real-time protection off | Easier to execute payloads undetected |
| VSS / Shadow Copies | Ransomware deletes these; forensics uses them for recovery |
| BitLocker without TPM | Startup key on USB = physical theft = full disk decryption |
| SmartScreen | Bypass by signing binaries or hosting on trusted sources |
| Firewall profiles | Public = most locked down; Domain = most permissive internally |