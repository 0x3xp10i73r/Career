# DOM Based Vulnerabilities

# DOM-Based Vulnerabilities

#### **Definition**

- Client-side vulnerabilities that occur when JavaScript takes attacker-controlled data (**source**) and passes it to a dangerous function or object (**sink**) without proper validation.
- Can lead to DOM XSS, open redirection, client-side injection, and other browser-side attacks.

#### How It Works

- Attacker controls input through a source.
- JavaScript reads the data from the source.
- Data is passed to a sink without proper validation or sanitization.
- The sink processes the data in an unsafe manner.
- Malicious actions execute within the victim's browser.

#### Detection / Mitigation

- Avoid passing untrusted data directly to sinks.
- Validate input using allowlists.
- Apply context-specific encoding and sanitization.
- Review client-side JavaScript for source-to-sink data flows.

---

# Taint Flow

#### **Definition**

- The flow of attacker-controlled data from a **source** to a **sink** within client-side JavaScript.
- Understanding taint flow is essential for identifying DOM-based vulnerabilities.

#### How It Works

- Data originates from a controllable source.
- Application processes the data.
- Data reaches a dangerous sink.
- Unsafe handling leads to exploitation.

#### Detection / Mitigation

- Map source-to-sink relationships during testing.
- Identify untrusted inputs reaching dangerous functions.
- Validate or sanitize data before sink usage.

---

# Sources

#### **Definition**

- JavaScript properties or objects that contain data potentially controlled by an attacker.

#### How It Works

- User-controlled input enters the application.
- JavaScript reads the data from a source.
- Data becomes part of the application's execution flow.

#### Common Sources

```jsx
location.search
document.referrer
document.cookie
window.name
localStorage
sessionStorage
history.pushState
history.replaceState
```

#### Detection / Mitigation

- Identify all attacker-controllable inputs.
- Treat source data as untrusted.
- Validate data before processing.

---

# Sinks

#### **Definition**

- JavaScript functions or DOM objects that can produce dangerous behavior when supplied with attacker-controlled input.

#### How It Works

- Application passes source data into a sink.
- Sink interprets or executes the data.
- Malicious code or actions occur if validation is absent.

#### Common Sinks

```jsx
eval()
document.write()
document.body.innerHTML
window.location
document.cookie
postMessage()
JSON.parse()
```

#### Detection / Mitigation

- Review all dangerous JavaScript APIs.
- Restrict untrusted data reaching sinks.
- Use safer alternatives where possible.

---

# Common DOM Vulnerability Sinks

#### **Definition**

- Different sinks lead to different classes of DOM-based vulnerabilities.

#### How It Works

- Attacker-controlled data reaches a sink.
- Sink determines the resulting vulnerability type.

#### Common Mappings

| Vulnerability | Sink |
| --- | --- |
| DOM XSS | `document.write()` |
| Open Redirection | `window.location` |
| Cookie Manipulation | `document.cookie` |
| JavaScript Injection | `eval()` |
| Document Domain Manipulation | `document.domain` |
| WebSocket URL Poisoning | `WebSocket()` |
| Link Manipulation | `element.src` |
| Web Message Manipulation | `postMessage()` |
| AJAX Header Manipulation | `setRequestHeader()` |
| Local File Path Manipulation | `FileReader.readAsText()` |
| Client-Side SQL Injection | `ExecuteSql()` |
| HTML5 Storage Manipulation | `sessionStorage.setItem()` |
| Client-Side XPath Injection | `document.evaluate()` |
| Client-Side JSON Injection | `JSON.parse()` |
| DOM Data Manipulation | `element.setAttribute()` |
| Denial of Service | `RegExp()` |

#### Detection / Mitigation

- Audit usage of dangerous sinks.
- Restrict attacker-controlled input.
- Apply context-aware validation.

---

# DOM Clobbering

**Definition**

- An advanced DOM attack where injected HTML modifies the DOM structure and changes JavaScript behavior.

### How It Works

- Attacker injects HTML into the page.
- HTML elements overwrite existing DOM properties or global variables.
- JavaScript references the manipulated object.
- Application behavior changes in an attacker-controlled manner.

### Detection / Mitigation

- Sanitize HTML input before rendering.
- Avoid relying on implicit global variables.
- Use strict DOM element references.
- Implement Content Security Policy (CSP).

---

# DOM-Based Open Redirection

**Definition**

- A DOM vulnerability where attacker-controlled data modifies the browser location and redirects users to malicious sites.

### How It Works

- Application reads a URL fragment.
- JavaScript validates it incorrectly.
- Browser location is updated using attacker-controlled data.
- Victim is redirected to an external site.

### Commands / Payloads

**Vulnerable Code**

```jsx
goto = location.hash.slice(1)

if (goto.startsWith('https:')) {
    location = goto;
}
```

**Malicious URL**

```
https://www.innocent-website.com/example#https://www.evil-user.net
```

### Detection / Mitigation

- Avoid assigning untrusted input directly to `window.location`.
- Use strict allowlists for redirect destinations.
- Validate URLs before redirection.