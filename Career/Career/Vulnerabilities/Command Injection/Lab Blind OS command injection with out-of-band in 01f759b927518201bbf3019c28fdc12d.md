# Lab: Blind OS command injection with out-of-band interaction

This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The command is executed asynchronously and has no effect on the application's response. It is not possible to redirect output into a location that you can access. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, exploit the blind OS command injection vulnerability to issue a DNS lookup to Burp Collaborator.

![image.png](Lab%20Blind%20OS%20command%20injection%20with%20out-of-band%20in/image.png)

![image.png](Lab%20Blind%20OS%20command%20injection%20with%20out-of-band%20in/image%201.png)

![image.png](Lab%20Blind%20OS%20command%20injection%20with%20out-of-band%20in/image%202.png)