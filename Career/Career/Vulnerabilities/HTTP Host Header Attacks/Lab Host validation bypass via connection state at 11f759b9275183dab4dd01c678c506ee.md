# Lab: Host validation bypass via connection state attack

This lab is vulnerable to routing-based SSRF via the Host header. Although the front-end server may initially appear to perform robust validation of the Host header, it makes assumptions about all requests on a connection based on the first request it receives.

To solve the lab, exploit this behavior to access an internal admin panel located at `192.168.0.1/admin`, then delete the user `carlos`.

![image.png](Lab%20Host%20validation%20bypass%20via%20connection%20state%20at/image.png)

![image.png](Lab%20Host%20validation%20bypass%20via%20connection%20state%20at/image%201.png)

![image.png](Lab%20Host%20validation%20bypass%20via%20connection%20state%20at/image%202.png)

![image.png](Lab%20Host%20validation%20bypass%20via%20connection%20state%20at/image%203.png)

![image.png](Lab%20Host%20validation%20bypass%20via%20connection%20state%20at/image%204.png)

![image.png](Lab%20Host%20validation%20bypass%20via%20connection%20state%20at/image%205.png)