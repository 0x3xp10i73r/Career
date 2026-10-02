# Lab: SSRF with filter bypass via open redirection vulnerability

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://192.168.0.12:8080/admin` and delete the user `carlos`.

The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first.

![image.png](Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20v/image.png)

![image.png](Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20v/image%201.png)

![image.png](Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20v/image%202.png)

![image.png](Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20v/image%203.png)

![image.png](Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20v/image%204.png)