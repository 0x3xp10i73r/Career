# Tools of the Trade

## AD Tools

### Unauthenticated / Initial Access

| Tool | Use |
| --- | --- |
| `Kerbrute` | Username enum, password spray (no lockout) |
| `Responder` | LLMNR/NBT-NS poisoning → capture hashes |
| `enum4linux-ng` | SMB/RPC enum without creds |
| `ldapsearch` | LDAP queries |
| `adidnsdump` | DNS zone dump |

### With Credentials

| Tool | Use |
| --- | --- |
| `BloodHound.py` | Map AD attack paths (Linux, no domain join needed) |
| `SharpHound` | BloodHound data collection (Windows) |
| `PowerView` | Manual AD enumeration / situational awareness |
| `CrackMapExec` | SMB/WMI/WinRM enum + attack |
| `windapsearch` | Automated LDAP queries |
| `Snaffler` | Find creds in file shares |
| `LAPSToolkit` | Audit/attack LAPS deployments |
| `smbmap` | Share enumeration |
| `DomainPasswordSpray.ps1` | Authenticated spray (removes near-lockout accounts) |

### Kerberos Attacks

| Tool | Use |
| --- | --- |
| `GetUserSPNs.py` | Kerberoasting |
| `GetNPUsers.py` | AS-REP Roasting |
| `Rubeus` | Full Kerberos abuse (tickets, spray, roast) |
| `ticketer.py` | Golden/Silver ticket creation |
| `raiseChild.py` | Child → parent domain privesc |

### Credential Dumping

| Tool | Use |
| --- | --- |
| `Mimikatz` | Pass-the-hash, cleartext creds, ticket extraction |
| `secretsdump.py` | Remote SAM/LSA/NTDS dump |
| `gpp-decrypt` | Decrypt Group Policy creds |
| `getnthash.py` | NT hash from TGT via U2U |

### Lateral Movement / Execution

| Tool | Use |
| --- | --- |
| `psexec.py` | Semi-interactive shell via SMB |
| `wmiexec.py` | Exec via WMI |
| `evil-winrm` | Interactive shell via WinRM |
| `mssqlclient.py` | MSSQL interaction |
| `smbserver.py` | Quick SMB server for file transfer |

### Exploit-Specific

| Tool | CVE |
| --- | --- |
| `noPac.py` | CVE-2021-42278 + 42287 → DA from user |
| `CVE-2021-1675.py` | PrintNightmare |
| `PetitPotam.py` | CVE-2021-36942 → auth coercion |
| `ntlmrelayx.py` | SMB relay |
| `gettgtpkinit.py` | Certificate + TGT abuse |

### Audit / Analysis

| Tool | Use |
| --- | --- |
| `BloodHound` (GUI) | Visual attack path mapping |
| `AD Explorer` | Browse AD, save snapshots, compare |
| `PingCastle` | AD security risk scoring |
| `Group3r` | GPO misconfiguration audit |
| `ADRecon` | Full AD data extract → Excel report |