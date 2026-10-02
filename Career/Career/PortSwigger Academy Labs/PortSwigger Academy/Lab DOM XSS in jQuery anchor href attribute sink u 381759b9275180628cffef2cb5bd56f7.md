# Lab: DOM XSS in jQuery anchor href attribute sink using location.search source

Level: APPRENTICE
Status: Done
Vulnerability: Cross-Site Scripting

This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's `$` selector function to find an anchor element, and changes its `href` attribute using data from `location.search`.

To solve this lab, make the "back" link alert `document.cookie`.

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image.png)

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image%201.png)

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image%202.png)