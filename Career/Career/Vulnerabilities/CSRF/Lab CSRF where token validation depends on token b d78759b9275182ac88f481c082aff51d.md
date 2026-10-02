# Lab: CSRF where token validation depends on token being present

This lab's email change functionality is vulnerable to CSRF.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: wiener:peter

1. Login with the given credentials

![image.png](Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20b/image.png)

1. Use the login functionality

![image.png](Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20b/image%201.png)

1. Intercept the corresponding request in the Proxy

 

![image.png](Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20b/image%202.png)

1. Send the request with the csrf token

![image.png](Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20b/image%203.png)

1. Send the request without the token (confirms csrf)

![image.png](Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20b/image%204.png)

1. Send the csrf payload through the exploit server

![image.png](Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20b/image%205.png)