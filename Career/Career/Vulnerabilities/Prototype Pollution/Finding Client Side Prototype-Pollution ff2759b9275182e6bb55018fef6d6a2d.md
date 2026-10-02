# Finding Client Side Prototype-Pollution

# 1. Finding **Prototype Pollution Sources** (Manual Method)

**Goal:**

Check whether you can add a fake property to `Object.prototype` from user input.

**Core Idea:**

Keep trying different inputs until one successfully pollutes the prototype.

It is mostly **trial and error**.

---

### Manual Steps

### Step 1 — Try Injecting a Property

Test all user-controlled inputs such as:

- URL **query string**
- URL **fragment / hash**
- **JSON inputs**
- Web messages / parameters

**Example Payload**

```
vulnerable-site.com/?__proto__[foo]=bar
```

---

### Step 2 — Verify in Browser Console

Open **DevTools → Console** and run:

```jsx
Object.prototype.foo
```

**Interpretation**

- `"bar"` → Prototype **successfully polluted**
- `undefined` → Pollution **failed**

---

### Step 3 — Try Different Syntax

If it fails, change the notation:

**Bracket notation**

```
?__proto__[foo]=bar
```

**Dot notation**

```
?__proto__.foo=bar
```

- Repeat for each parameter and input location.
- This process is **repetitive but necessary**.

---

# 2. Finding Sources Using **DOM Invader** (Automated Method)

Manual testing can be slow and tiring.

**DOM Invader (Burp Suite built-in browser) helps by:**

- Automatically testing parameters
- Detecting prototype pollution sources
- Reducing manual effort and time

**Best Used When**

- Website is large
- Many parameters exist
- Quick results are needed

---

# 3. Finding **Prototype Pollution Gadgets** (Manual Method)

**Goal:**

After confirming pollution works, find a **property that the application uses dangerously**.

A gadget is a polluted property that reaches a **dangerous sink** like:

- `innerHTML`
- `eval`
- script loading
- redirects

---

### Manual Gadget Discovery Steps

### Step 1 — Review Source Code

Look for properties used in:

- DOM updates
- URL building
- Script sources
- Function execution

---

### Step 2 — Intercept JavaScript in Burp

- Go to **Proxy → Options**
- Enable **Intercept Server Responses**
- Capture the JavaScript file

---

### Step 3 — Insert a Debugger

Add at the top of the script:

```jsx
debugger;
```

- This pauses execution in the browser.

---

### Step 4 — Add a Prototype Trap in Console

While paused, run:

```jsx
Object.defineProperty(Object.prototype, 'testProp', {
  get() {
    console.trace();
    return 'polluted';
  }
});
```

**What This Does**

- Adds a fake property globally
- Logs a stack trace whenever it is accessed

---

### Step 5 — Resume Execution

- Click **Continue**
- Watch the console output

**If a Stack Trace Appears**

- The property is being accessed
- Possible **gadget found**

---

### Step 6 — Investigate the Code

- Click the stack trace link
- Check where the property is used
- See if it reaches dangerous sinks like:
    - `innerHTML`
    - `eval`
    - `script.src`
    - redirects

---

### Step 7 — Repeat

- Try multiple property names
- Identify the best exploitation path

---

# 4. Finding Gadgets Using **DOM Invader** (Automated)

Manual gadget hunting is **time-consuming** because:

- Websites use many third-party libraries
- Code may be minified or obfuscated
- Thousands of lines to inspect

**DOM Invader Can**

- Automatically scan for gadgets
- Highlight dangerous property usage
- Sometimes generate **DOM XSS PoC automatically**

---

## Simple Understanding Summary

- **Source → Where pollution enters**
- **Gadget → Where pollution is used dangerously**
- **Manual → Trial, console checks, debugger**
- **DOM Invader → Fast automated detection**

---