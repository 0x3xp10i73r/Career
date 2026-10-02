# Lab: Stored XSS into anchor href attribute with double quotes HTML-encoded

This lab contains a stored cross-site scripting vulnerability in the comment functionality. To solve this lab, submit a comment that calls the alert function when the comment author name is clicked. 

# Notes

**“Stored XSS into anchor `href` attribute with double quotes HTML-encoded.”**

---

- Input is stored in the application (e.g., comments, profiles, posts) and later rendered to other users.
- The stored value is reflected inside an anchor tag’s `href` attribute, like:
    
    ```html
    <a href="REFLECTED-HERE">link</a>
    ```
    
- Double quotes in the input are HTML-encoded to `&quot;`, so breaking out of the attribute using `"` is blocked.
- Only angle-bracket or quote injection is prevented — the attribute value itself is still fully controllable.
- Because the value stays inside `href`, injecting a JavaScript URL scheme is still possible:
    
    ```jsx
    javascript:alert(1)
    
    ```
    
- The resulting HTML becomes:
    
    ```html
    <a href="javascript:alert(1)">link</a>
    
    ```
    
- The payload executes when the link is triggered (clicked or programmatically activated).
- This is stored XSS: the payload persists in the database and affects every user who views the page.
- Key reason it works: encoding quotes is not enough — URL schemes in attributes must be validated/blocked.
- Correct defenses include:
    - Reject or strictly whitelist URL schemes (`http`, `https` only).
    - Use context-aware encoding for attributes.
    - Prefer server-side validation for stored fields.

![image.png](Lab%20Stored%20XSS%20into%20anchor%20href%20attribute%20with%20dou/image.png)

![image.png](Lab%20Stored%20XSS%20into%20anchor%20href%20attribute%20with%20dou/image%201.png)

![image.png](Lab%20Stored%20XSS%20into%20anchor%20href%20attribute%20with%20dou/image%202.png)