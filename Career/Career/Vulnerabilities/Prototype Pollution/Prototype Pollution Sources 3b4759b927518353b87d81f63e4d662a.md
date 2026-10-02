# Prototype Pollution Sources

## Prototype Pollution Sources

- A **prototype pollution source** is any **user-controllable input** that allows attackers to inject arbitrary properties into **JavaScript prototype objects**.
- These inputs become dangerous when they are:
    - Parsed into objects
    - Merged into existing objects
    - Used without proper key validation

---

## Common Prototype Pollution Sources

### 1. URL Parameters (Query String / Fragment)

- User-controlled values in:
    - Query string (`?key=value`)
    - URL fragment (`#key=value`)
- Often parsed automatically into key:value objects by frameworks or libraries.

---

## Prototype Pollution via URL Parameters

### Example Malicious URL

```
https://vulnerable-website.com/?__proto__[evilProperty]=payloa
```

### What Developers Expect

- The parsed object might look like:

```jsx
{
  existingProperty1: "foo",
  existingProperty2: "bar",
  __proto__: {
    evilProperty: "payload"
  }
}
```

### What Actually Happens (Important)

- During recursive merge operations, the application may execute logic equivalent to:

```jsx
targetObject.__proto__.evilProperty = "payload";
```

- JavaScript treats `__proto__` as a **getter for the prototype**, not a normal property.
- As a result:
    - `evilProperty` is added to **Object.prototype**
    - Not to the target object itself

### Impact

- All objects in the runtime now inherit `evilProperty`
- This affects:
    - Existing objects
    - Future objects
- If the polluted property is used by application logic or libraries, this can lead to:
    - Security bypass
    - Logic manipulation
    - Code execution (in extreme cases)

---

## Prototype Pollution via JSON Input

- Many applications convert user input into objects using:

```jsx
JSON.parse()
```

- `JSON.parse()` treats **all keys as plain strings**, including:
    - `__proto__`
    - `constructor`
    - `prototype`

---

## Malicious JSON Example

```json
{
  "__proto__": {
    "evilProperty": "payload"
  }
}
```

### Resulting JavaScript Objects

```jsx
const objectLiteral = { __proto__: { evilProperty: "payload" } };
const objectFromJson = JSON.parse('{"__proto__": {"evilProperty": "payload"}}');

objectLiteral.hasOwnProperty("__proto__");   // false
objectFromJson.hasOwnProperty("__proto__");  // true
```

### Why This Matters

- Object literals treat `__proto__` as a prototype setter
- `JSON.parse()` creates a **real own property** named `__proto__`
- If this parsed object is later merged into another object:
    - Prototype pollution occurs
    - Same impact as URL-based injection

---

## Why These Sources Are Dangerous

- User input becomes **object structure**
- Unsafe merge operations propagate attacker-controlled keys
- Prototype-level changes affect the **entire application**
- Often invisible during normal testing