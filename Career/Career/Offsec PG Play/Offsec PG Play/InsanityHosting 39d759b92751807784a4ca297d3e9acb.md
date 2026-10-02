# InsanityHosting

Date: July 14, 2026
Difficulty: Intermediate
Status: Not started

## Nmap Scan

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -p- --min-rate=1000 -oA nmap/insanityHosting 192.168.200.124
Starting Nmap 7.99 ( https://nmap.org ) at 2026-07-14 00:04 -0400
Stats: 0:01:27 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 65.70% done; ETC: 00:07 (0:00:45 remaining)
Nmap scan report for 192.168.200.124
Host is up (0.074s latency).
Not shown: 65396 filtered tcp ports (no-response), 136 filtered tcp ports (host-prohibited)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: ERROR
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.45.205
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 85:46:41:06:da:83:04:01:b0:e4:1f:9b:7e:8b:31:9f (RSA)
|   256 e4:9c:b1:f2:44:f1:f0:4b:c3:80:93:a9:5d:96:98:d3 (ECDSA)
|_  256 65:cf:b4:af:ad:86:56:ef:ae:8b:bf:f2:f0:d9:be:10 (ED25519)
80/tcp open  http    Apache httpd 2.4.6 ((CentOS) PHP/7.2.33)
|_http-title: Insanity - UK and European Servers
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) PHP/7.2.33
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 149.08 seconds

```

## Fuzzing Directories

```bash
┌──(kali㉿kali)-[~]
└─$ ffuf -u "http://192.168.200.124/FUZZ" -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -r

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.200.124/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.htaccess               [Status: 403, Size: 211, Words: 15, Lines: 9, Duration: 63ms]
.htpasswd               [Status: 403, Size: 211, Words: 15, Lines: 9, Duration: 3271ms]
cgi-bin/                [Status: 403, Size: 210, Words: 15, Lines: 9, Duration: 66ms]
css                     [Status: 200, Size: 2397, Words: 202, Lines: 23, Duration: 64ms]
data                    [Status: 200, Size: 1091, Words: 117, Lines: 17, Duration: 61ms]
fonts                   [Status: 200, Size: 2915, Words: 205, Lines: 25, Duration: 61ms]
img                     [Status: 200, Size: 1091, Words: 107, Lines: 17, Duration: 85ms]
js                      [Status: 200, Size: 4225, Words: 338, Lines: 31, Duration: 62ms]
licence                 [Status: 200, Size: 57, Words: 10, Lines: 2, Duration: 66ms]
monitoring              [Status: 200, Size: 4848, Words: 110, Lines: 96, Duration: 60ms]
news                    [Status: 200, Size: 5111, Words: 362, Lines: 136, Duration: 119ms]
phpmyadmin              [Status: 200, Size: 15373, Words: 2711, Lines: 322, Duration: 228ms]
readme.md               [Status: 200, Size: 2599, Words: 508, Lines: 80, Duration: 66ms]
webmail                 [Status: 200, Size: 2896, Words: 297, Lines: 77, Duration: 80ms]
:: Progress: [20481/20481] :: Job [1/1] :: 550 req/sec :: Duration: [0:00:38] :: Errors: 0 ::
                                                                 
```

## Nuclei Scan

```bash
──(kali㉿kali)-[~]
└─$ nuclei -u http://192.168.200.124           

                     __     _
   ____  __  _______/ /__  (_)
  / __ \/ / / / ___/ / _ \/ /
 / / / / /_/ / /__/ /  __/ /
/_/ /_/\__,_/\___/_/\___/_/   v3.10.0

                projectdiscovery.io

[INF] Current nuclei version: v3.10.0 (outdated)
[INF] Current nuclei-templates version: v10.4.5 (latest)
[INF] New templates added in latest release: 86
[INF] Templates loaded for current scan: 10447
[INF] Executing 10430 signed templates from projectdiscovery/nuclei-templates
[INF] Targets loaded for current scan: 1
[INF] Templates clustered: 2389 (Reduced 2256 Requests)
[INF] Using Interactsh Server: oast.me
[phpmyadmin-panel] [http] [info] http://192.168.200.124/phpmyadmin/ ["5.0.2"] [paths="/phpmyadmin/"]
[phpinfo-files] [http] [low] http://192.168.200.124/phpinfo.php ["7.2.33"] [paths="/phpinfo.php"]
[http-trace:trace-request] [http] [info] http://192.168.200.124
[http-trace:options-request] [http] [info] http://192.168.200.124
[waf-detect:apachegeneric] [http] [info] http://192.168.200.124
[CVE-2023-48795] [javascript] [medium] 192.168.200.124:22 ["Vulnerable to Terrapin"]
[ssh-auth-methods] [javascript] [info] 192.168.200.124:22 ["["publickey","gssapi-keyex","gssapi-with-mic","password"]"]
[ssh-diffie-hellman-logjam] [javascript] [low] 192.168.200.124:22
[ssh-password-auth] [javascript] [info] 192.168.200.124:22
[ssh-server-enumeration] [javascript] [info] 192.168.200.124:22 ["SSH-2.0-OpenSSH_7.4"]
[ssh-sha1-hmac-algo] [javascript] [info] 192.168.200.124:22
[ssh-cbc-mode-ciphers] [javascript] [low] 192.168.200.124:22
[ssh-weakkey-exchange-algo] [javascript] [low] 192.168.200.124:22
[CVE-2015-1419:version] [tcp] [medium] 192.168.200.124:21 ["3.0.2"]
[CVE-2021-30047:version] [tcp] [high] 192.168.200.124:21 ["3.0.2"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="123456",username="ftp"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="stingray",username="ftp"]
[ftp-anonymous-login] [tcp] [medium] 192.168.200.124:21
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="pass1",username="ftp"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="default",username="ftp"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="password",username="ftp"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="toor",username="ftp"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="guest",username="ftp"]
[ftp-weak-credentials] [tcp] [high] 192.168.200.124:21 [password="nas",username="ftp"]
[ftp-detect] [tcp] [info] 192.168.200.124:21
[vsftpd-detect:version] [tcp] [info] 192.168.200.124:21 ["3.0.2"]
[openssh-detect] [tcp] [info] 192.168.200.124:22 ["SSH-2.0-OpenSSH_7.4"]
[package-json] [http] [info] http://192.168.200.124/package.json
[package-json] [http] [info] http://192.168.200.124/package-lock.json
[apache-detect] [http] [info] http://192.168.200.124 ["Apache/2.4.6 (CentOS) PHP/7.2.33"]
[php-eol:version] [http] [info] http://192.168.200.124 ["7.2.33"]
[php-detect] [http] [info] http://192.168.200.124
[http-missing-security-headers:x-permitted-cross-domain-policies] [http] [info] http://192.168.200.124
[http-missing-security-headers:referrer-policy] [http] [info] http://192.168.200.124
[http-missing-security-headers:cross-origin-embedder-policy] [http] [info] http://192.168.200.124
[http-missing-security-headers:cross-origin-opener-policy] [http] [info] http://192.168.200.124
[http-missing-security-headers:strict-transport-security] [http] [info] http://192.168.200.124
[http-missing-security-headers:content-security-policy] [http] [info] http://192.168.200.124
[http-missing-security-headers:x-content-type-options] [http] [info] http://192.168.200.124
[http-missing-security-headers:cross-origin-resource-policy] [http] [info] http://192.168.200.124
[http-missing-security-headers:permissions-policy] [http] [info] http://192.168.200.124
[http-missing-security-headers:x-frame-options] [http] [info] http://192.168.200.124
[squirrelmail-login] [http] [info] http://192.168.200.124/webmail/src/login.php
[INF] Skipped 192.168.200.124:5814 from target list as found unresponsive permanently: Get "https://192.168.200.124:5814/autopass": cause="port closed or filtered" address=192.168.200.124:5814 chain="no route to host"
[INF] Skipped 192.168.200.124:4040 from target list as found unresponsive permanently: cause="port closed or filtered" address=192.168.200.124:4040 chain="no route to host; got err while executing http://192.168.200.124:4040/jobs/"
[options-method] [http] [info] http://192.168.200.124 ["OPTIONS,GET,HEAD,POST,TRACE"]
[centos-eol] [http] [info] http://192.168.200.124
[fingerprinthub-web-fingerprints:openfire] [http] [info] http://192.168.200.124
[INF] Scan completed in 2m. 46 matches found.
[INF] HTTP connections: 13236 total, 2573 new, 10663 reused (80.6%)

```

## Web Pages

![image.png](InsanityHosting/image.png)

![image.png](InsanityHosting/image%201.png)

![image.png](InsanityHosting/image%202.png)

![image.png](InsanityHosting/image%203.png)