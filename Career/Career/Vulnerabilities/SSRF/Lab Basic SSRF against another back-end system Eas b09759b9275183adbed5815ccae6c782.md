# Lab: Basic SSRF against another back-end system | Easy

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, use the stock check functionality to scan the internal `192.168.0.X` range for an admin interface on port
`8080`, then use it to delete the user `carlos`.

1. Visit the lab and click on any product and view details

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image.png)

1. Click on check stock button and intercept the request in the proxy

![image.png](Lab%20Basic%20SSRF%20against%20the%20local%20server%20Easy/image%201.png)

1. Intercept the request in burpsuite

![image.png](Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20Eas/image.png)

1. Send the request in the intruder by and setup payload for the IP using /admin 

![image.png](Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20Eas/image%201.png)

![image.png](Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20Eas/image%202.png)

![image.png](Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20Eas/image%203.png)

![image.png](Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20Eas/image%204.png)