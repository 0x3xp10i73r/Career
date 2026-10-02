# Lab: Username enumeration via subtly different responses

This lab is subtly vulnerable to username enumeration and password 
brute-force attacks. It has an account with a predictable username and 
password, which can be found in the following wordlists:

- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.

1. Start the sniper attack from the intruder and observe the response

![image.png](Lab%20Username%20enumeration%20via%20subtly%20different%20resp/image.png)

1. Perform the negative search of the responses for error response `“Invalid username or password.”` from the intruder attack. You’ll see the valid username

![image.png](Lab%20Username%20enumeration%20via%20subtly%20different%20resp/image%201.png)

1. With valid username start the intruder sniper attack for the password parameter.

![image.png](Lab%20Username%20enumeration%20via%20subtly%20different%20resp/image%202.png)

![image.png](Lab%20Username%20enumeration%20via%20subtly%20different%20resp/image%203.png)