# JavaScript Objects, Prototypes & Prototype Pollution

## What is Prototype Pollution?

- **Prototype Pollution** is a JavaScript vulnerability where an attacker can **inject properties into global object prototypes**.
- These properties are then **inherited by all objects** in the application.
- This allows attackers to **manipulate object behavior indirectly**.

### Why Prototype Pollution Is Dangerous

- Often **not exploitable alone**, but very powerful when chained.
- Can lead to:
    - DOM-based XSS (client-side)
    - Authentication or authorization bypass
    - Business logic manipulation
    - Remote Code Execution (server-side JavaScript / Node.js)

---

## What is an Object in JavaScript?

- A JavaScript object is a collection of **key : value pairs**, called **properties**.
- Objects are used to represent real-world entities (users, configs, data).

### Example Object

```jsx
const user = {
  username: "wiener",
  userId: 1234,
  isAdmin: false
};
```

### Accessing Object Properties

- **Dot notation**

```jsx
user.username;
```

- **Bracket notation**

```jsx
user['userId'];
```

---

## Methods in JavaScript Objects

- Object properties can also contain **functions**, called **methods**.

### Example

```jsx
const user = {
  username: "wiener",
  exampleMethod: function () {
    // do something
  }
};
```

---

## Important Concept: Almost Everything Is an Object

- In JavaScript:
    - Arrays
    - Strings
    - Numbers
    - Functions
        
        are all objects **under the hood**.
        
- When we say “object”, we are not only talking about `{}` literals.

---

## What is a Prototype?

- Every JavaScript object is linked to another object called its **prototype**.
- Prototypes provide **shared properties and methods**.
- If a property is not found on the object itself, JavaScript **looks it up in the prototype**.

---

## Built-in Prototypes (Examples)

```jsx
let obj = {};
Object.getPrototypeOf(obj);     // Object.prototype

let str = "";
Object.getPrototypeOf(str);     // String.prototype

let arr = [];
Object.getPrototypeOf(arr);     // Array.prototype
```

- These prototypes are **global** and shared.

---

## Property Lookup Mechanism (Very Important)

Whenever you access a property:

1. JavaScript checks the **object itself**
2. If not found, it checks the **prototype**
3. Continues up the **prototype chain**
4. Stops when found or when it reaches `null`

---

## Example: Empty Object Is Not Really Empty

```jsx
let myObject = {};
```

- `myObject` has **no own properties**
- But you can still do:

```jsx
myObject.toString();
myObject.hasOwnProperty("test");
```

- These methods come from **Object.prototype**

### Browser Console Observation

- Typing `myObject.` shows many methods
- These are **inherited**, not defined directly

---

## The Prototype Chain

- A prototype is **just another object**
- That object also has its own prototype
- This forms a **prototype chain**

### Prototype Chain Flow

```
Object → Prototype → Prototype → Object.prototype → null
```

- `Object.prototype` is the **top-level prototype**
- Its prototype is `null` (end of chain)

---

## Inheritance from the Entire Chain

- Objects inherit properties from:
    - Their immediate prototype
    - Every prototype **above it**

### Example

- A string object inherits from:
    - `String.prototype`
    - `Object.prototype`

This is why:

```jsx
"ADMIN".toLowerCase();
```

works automatically.

---

## Accessing an Object’s Prototype (`__proto__`)

- Every object has a special property called `__proto__`
- It acts as:
    - A **getter** (read prototype)
    - A **setter** (modify prototype)

### Accessing `__proto__`

```jsx
username.__proto__;
username['__proto__'];
```

---

## Walking the Prototype Chain with `__proto__`

```jsx
username.__proto__;                        // String.prototype
username.__proto__.__proto__;              // Object.prototype
username.__proto__.__proto__.__proto__;    // null
```

---

## Why `__proto__` Is Security-Critical

- Modifying `__proto__` modifies the **prototype**
- Prototypes are **shared globally**
- Polluting them affects:
    - Existing objects
    - Future objects

This is the **core reason prototype pollution works**.

---

## Prototype Pollution in Practice

- Happens when user input is **merged into objects unsafely**
- Commonly abused keys:
    - `__proto__`
    - `constructor`
    - `prototype`

### Example Impact

- Attacker adds a hidden property like:

```jsx
isAdmin = true
```

- Every object now inherits it
- Security checks may be bypassed

---