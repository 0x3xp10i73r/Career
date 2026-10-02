# Lab: DOM XSS in document.write sink using source location.search inside a select element

Level: PRACTITIONER
Status: Done
Vulnerability: Cross-Site Scripting

This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search` which you can control using the website URL. The data is enclosed within a select element.

To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the alert function.

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo%203987-e96a/image.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo%203987-e96a/image%201.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo%203987-e96a/image%202.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo%203987-e96a/image%203.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo%203987-e96a/image%204.png)