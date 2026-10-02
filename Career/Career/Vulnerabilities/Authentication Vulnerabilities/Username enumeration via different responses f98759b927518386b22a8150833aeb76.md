# Username enumeration via different responses

This lab is vulnerable to username enumeration and password 
brute-force attacks. It has an account with a predictable username and 
password, which can be found in the following wordlists:

- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.

1. Brute-force the username with the Sniper attack in the intruder & get the valid Username

![image.png](Username%20enumeration%20via%20different%20responses/image.png)

1. Now after valid username, brute-force the passwords using the sniper attack.

![image.png](Username%20enumeration%20via%20different%20responses/image%201.png)

![image.png](Username%20enumeration%20via%20different%20responses/image%202.png)

![image.png](Username%20enumeration%20via%20different%20responses/image%203.png)