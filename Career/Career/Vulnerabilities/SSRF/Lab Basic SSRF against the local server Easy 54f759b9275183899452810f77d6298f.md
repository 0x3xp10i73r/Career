# Lab: Basic SSRF against the local server | Easy

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

1. Visit the lab and click on any product and view details

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image.png)

1. Click on check stocke button and intercept the request in the proxy

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image%201.png)

1. Craft the SSRF payload on the stockApi parameter & get the delete function

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image%202.png)

1. Call that function

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image%203.png)

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image%204.png)