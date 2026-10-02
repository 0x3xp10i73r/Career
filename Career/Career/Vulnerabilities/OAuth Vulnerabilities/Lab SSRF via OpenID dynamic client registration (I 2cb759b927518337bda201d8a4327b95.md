# Lab: SSRF via OpenID dynamic client registration (Incomplete)

This lab allows client applications to dynamically register themselves with the OAuth service via a dedicated registration endpoint. Some client-specific data is used in an unsafe way by the OAuth service, which exposes a potential vector for SSRF.

To solve the lab, craft an SSRF attack to access [http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/](http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/) and steal the secret access key for the OAuth provider's cloud environment.

You can log in to your own account using the following credentials: wiener:peter
Note

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

![image.png](Lab%20SSRF%20via%20OpenID%20dynamic%20client%20registration%20(I/image.png)

![image.png](Lab%20SSRF%20via%20OpenID%20dynamic%20client%20registration%20(I/image%201.png)

![image.png](Lab%20SSRF%20via%20OpenID%20dynamic%20client%20registration%20(I/image%202.png)

![image.png](Lab%20SSRF%20via%20OpenID%20dynamic%20client%20registration%20(I/image%203.png)