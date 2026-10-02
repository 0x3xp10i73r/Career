# Lab: DOM XSS in jQuery anchor href attribute sink using location.search source

# Notes

- A URL is created where a query parameter contains a payload, such as `?path=javascript:alert(1)`.
- The page loads and its script reads the parameter from `location.search`.
- The parameter value is inserted directly into an anchor tag using `$('a').attr('href', value)`.
- The anchor element in the DOM now contains a `href` pointing to a `javascript:` payload.
- The browser interprets the `href` as executable code rather than a normal link.
- When the link is triggered (clicked or programmatically activated), the payload executes within the page context.

This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's $ selector function to find an anchor element, and changes its href attribute using data from location.search.

To solve this lab, make the "back" link alert document.cookie.

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image.png)

1. Edit the link of back button & click on the back button

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image%201.png)

1. Exploit the XSS

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image%202.png)

![image.png](Lab%20DOM%20XSS%20in%20jQuery%20anchor%20href%20attribute%20sink%20u/image%203.png)