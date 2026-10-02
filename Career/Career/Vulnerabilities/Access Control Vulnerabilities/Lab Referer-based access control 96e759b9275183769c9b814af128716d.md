# Lab: Referer-based access control

1. Log in using the admin credentials.
2. Browse to the admin panel, promote `carlos`, and send the HTTP request to Burp Repeater.
3. Open a private/incognito browser window, and log in with the non-admin credentials.
4. Browse to `/admin-roles?username=carlos&action=upgrade` and observe that the request is treated as unauthorized due to the absent Referer header.
5. Copy the non-admin user's session cookie into
the existing Burp Repeater request, change the username to yours, and
replay it.