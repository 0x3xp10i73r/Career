# Bypassing SameSite=Strict via On-Site Gadgets

**Core Concept:**

- **SameSite=Strict** blocks ALL cross-site cookie sending
- But allows cookies in **same-site requests**
- Find **on-site gadget** that triggers secondary request
- Manipulate gadget to perform malicious action

---

**Attack Flow:**

1. **Find gadget** on target site that makes internal requests
2. **Gadget must be controllable** (parameters, input)
3. **Trigger gadget** via cross-site request (no cookies sent)
4. **Gadget executes** secondary request **on-site** (cookies included)
5. **Malicious action** performed with full session

---

**Common Gadgets:**

**1. Client-Side Redirects:**

```jsx
// Vulnerable code on target.com
const url = new URLSearchParams(window.location.search).get('redirect');
window.location = url;  // Controlled by attacker
```

**2. Dynamic Resource Loading:**

```jsx
// Load user-controlled resource
const img = new Image();
img.src = getUserAvatar();  // Attacker controls return value
```

**3. JSONP Endpoints:**

```jsx
callback = function(data) {
  // Makes follow-up requests with cookies
  fetch('/update-profile', {method: 'POST', body: data});
}
```

**4. Post-Message Handlers:**

```jsx
window.addEventListener('message', (e) => {
  // Trusted origin check missing
  fetch(e.data.url);  // Attacker controls
});
```

---

**Example Attack:**

**Step 1:** Find redirect gadget at `https://bank.com/redirect?to=/dashboard`**Step 2:** Craft exploit:

```
<https://bank.com/redirect?to=/transfer?to=attacker&amount=1000>
```

**Step 3:** Victim visits (cross-site) → No cookies sent
**Step 4:** Bank's redirect executes → Makes **same-site** request to `/transfer`**Step 5:** Cookies included → Transfer completes

---

**Key Requirements:**

- ✅ Gadget exists on target domain
- ✅ Gadget accepts attacker-controlled input
- ✅ Gadget triggers secondary request
- ✅ Secondary request is **same-site** (includes cookies)

---

**Impact:**

- **Bypasses SameSite=Strict completely**
- **Full CSRF capability** restored
- **High risk** if vulnerable gadget exists

---

**Testing Methodology:**

1. **Search for client-side redirects**
    - `window.location`
    - `document.location`
    - `location.href`
    - `location.replace()`
2. **Look for dynamic URL construction**
    
    ```jsx
    fetch('/api/' + userInput)
    img.src = userControlledUrl
    ```
    
3. **Test parameter control**
    - URL parameters
    - Fragment identifiers
    - Post-message data
    - Referrer headers

---