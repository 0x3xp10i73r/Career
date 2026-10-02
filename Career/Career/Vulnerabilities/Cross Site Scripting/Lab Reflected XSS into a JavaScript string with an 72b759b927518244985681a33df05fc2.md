# Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function. 

# Notes

**“Reflected XSS into a JavaScript string with angle brackets HTML-encoded.”**

---

- The application reflects user input inside a JavaScript string literal in the HTML response.
    
    Example pattern in the page source:
    
    ```html
    <script>
        var msg = 'REFLECTED-HERE';
    </script>
    ```
    
- `<` and `>` are HTML-encoded (e.g., `&lt;`, `&gt;`), so new HTML/`<script>` tags cannot be injected.
- However, the input is still inside a JavaScript context, not an HTML context.
- The main weakness: the value can break out of the JavaScript string using the surrounding quote.
    - If the string uses `'...'`, test with `'`
    - If the string uses `"..."`, test with `"`
- After breaking out of the string, additional JavaScript can be injected.
    
    Typical payload pattern:
    
    ```jsx
    ');alert(1);//
    
    ```
    
- Angle-bracket encoding does not protect against this because injection happens before HTML is parsed — it happens in the script interpreter.
- Execution happens as soon as the script runs, without user interaction.
- Key testing steps:
    - Identify quote type around the reflected value.
    - Inject a matching quote to terminate the string.
    - Add valid JavaScript code.
    - Comment out the remainder (`//` or `/* */`) to avoid syntax errors.
- Core lesson:
    
    **Encoding `<` and `>` is insufficient — JavaScript contexts require JavaScript-aware escaping.**
    
- Proper defenses:
    - Use context-aware encoding for JavaScript (`JSON.stringify()`style escaping).
    - Avoid inline JavaScript when possible.
    - Treat reflected parameters as untrusted input.

![image.png](Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20an/image.png)

![image.png](Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20an/image%201.png)