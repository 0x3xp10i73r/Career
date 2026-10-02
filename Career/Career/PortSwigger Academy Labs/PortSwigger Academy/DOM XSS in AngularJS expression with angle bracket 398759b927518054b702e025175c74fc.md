# DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

Level: PRACTITIONER
Status: Done
Vulnerability: Cross-Site Scripting

This lab contains a DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality.

AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the ng-app attribute (also known as an AngularJS directive). When a directive is added to the HTML code, you can execute JavaScript expressions within double curly braces. This technique is useful when angle brackets are being encoded.

To solve this lab, perform a cross-site scripting attack that executes an AngularJS expression and calls the alert function.

![image.png](DOM%20XSS%20in%20AngularJS%20expression%20with%20angle%20bracket/image.png)