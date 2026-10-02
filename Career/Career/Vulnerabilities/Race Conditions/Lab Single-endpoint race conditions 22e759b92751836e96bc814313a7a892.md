# Lab: Single-endpoint race conditions

This lab's email change feature contains a race condition that enables you to associate an arbitrary email address with your account.

Someone with the address `carlos@ginandjuice.shop` has a pending invite to be an administrator for the site, but they have not yet created an account. Therefore, any user who successfully claims this address will automatically inherit admin privileges.

To solve the lab:

1. Identify a race condition that lets you claim an arbitrary email address.
2. Change your email address to `carlos@ginandjuice.shop`.
3. Access the admin panel.
4. Delete the user `carlos`

You can log in to your own account with the following credentials: `wiener:peter`.

You also have access to an email client, where you can view all emails sent to `@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net` addresses.

![image.png](Lab%20Single-endpoint%20race%20conditions/image.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%201.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%202.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%203.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%204.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%205.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%206.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%207.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%208.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%209.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%2010.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%2011.png)

![image.png](Lab%20Single-endpoint%20race%20conditions/image%2012.png)