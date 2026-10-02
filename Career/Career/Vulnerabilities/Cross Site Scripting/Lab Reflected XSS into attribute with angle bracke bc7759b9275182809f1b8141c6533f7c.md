# Lab: Reflected XSS into attribute with angle brackets HTML-encoded

# Notes

- The application reflects user input inside an HTML attribute (e.g., `value=""`, `src=""`, `title=""`).
- `<` and `>` characters are HTML-encoded (e.g., `&lt;` and `&gt;`), so injecting full `<script>` tags is blocked.
- Even though tags cannot be injected, the attribute context is still controllable.
- The payload can break out of the existing attribute value using a quote (`"`) or (`'`), depending on how the attribute is wrapped.
- Once outside the attribute value, new attributes can be added — especially event-handler attributes such as:
    - `onmouseover=...`
    - `onclick=...`
    - `onfocus=...`
- Example reflection pattern (simplified):
    
    ```html
    <input value="REFLECTED-HERE">
    ```
    
    Payload pattern:
    
    ```jsx
    " autofocus onfocus=alert(1) "
    ```
    
- Because `<` and `>` are encoded, this does not rely on creating new tags — only on modifying attributes.
- Execution occurs when the event is triggered (e.g., focus, click, mouse movement).
- This is considered reflected DOM XSS because the value is reflected immediately in the HTML response.
- Key testing strategy:
    - Identify the attribute context.
    - Determine the quote used (`"` vs `'`).
    - Inject a closing quote, then add an event handler.
- Key lesson: encoding angle brackets alone is insufficient — attribute context still requires proper output encoding and validation.

![image.png](Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20bracke/image.png)

![image.png](Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20bracke/image%201.png)

![image.png](Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20bracke/image%202.png)

![image.png](Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20bracke/image%203.png)

```jsx
" autofocus onfocus=alert(document.domain) x="
```

![image.png](Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20bracke/image%204.png)