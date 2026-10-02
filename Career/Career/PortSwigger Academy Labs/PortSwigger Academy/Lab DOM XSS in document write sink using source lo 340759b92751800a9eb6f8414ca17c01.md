# Lab: DOM XSS in document.write sink using source location.search

Level: APPRENTICE
Status: Done
Vulnerability: Cross-Site Scripting

This lab contains a DOM-based cross-site scripting  vulnerability in the search query tracking functionality. It uses the  JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search`, which you can control using the website URL.

To solve this lab, perform a cross-site scripting attack that calls the `alert` function.

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image%201.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image%202.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image%203.png)