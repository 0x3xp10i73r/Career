# SQL Injection Filter Evasion

**Definition**

- Techniques used to **bypass input filtering or sanitisation mechanisms** that attempt to block SQL injection payloads.
- Useful when applications implement **keyword filtering, character removal, or input validation**.

### How It Works

- Web applications filter common SQL keywords such as `OR`, `AND`, `SELECT`, and `UNION`.
- Attackers modify payloads to **avoid detection by the filter**.
- The database eventually **decodes or interprets the modified payload**, allowing the SQL injection to execute.
- Common evasion techniques include:
    - Character encoding
    - Operator substitution
    - Removing quotes
    - Avoiding spaces

### Detection / Mitigation

- Use **parameterized queries or prepared statements**.
- Avoid filtering SQL keywords using string replacement.
- Implement **server-side input validation and strict query handling**.
- Apply **WAF rules capable of decoding encoded payloads**.

---

# Character Encoding for SQL Injection Bypass

**Definition**

- A filter evasion technique where **special characters or SQL keywords are encoded** so that input filters fail to recognise them as malicious.

### How It Works

- The attacker encodes characters before sending them to the server.
- Application filters process the encoded input without detecting SQL keywords.
- The server or database **decodes the payload during processing**.
- The decoded payload executes as a valid SQL injection.

### Detection / Mitigation

- Normalize and decode inputs **before validation**.
- Use prepared statements.
- Monitor encoded input patterns in HTTP requests.

---

# URL Encoding SQL Injection

**Definition**

- Encoding characters using `%` followed by their **ASCII hexadecimal value** to bypass keyword filters.

### How It Works

- SQL keywords or characters are URL encoded.
- Filters that check for plain text SQL keywords fail to detect them.
- The web server decodes the payload before executing the SQL query.

### Commands / Payloads

**Normal Payload**

```sql
' OR 1=1 --
```

**URL Encoded Payload**

```
%27%20OR%201%3D1--
```

**Filter Evasion Payload Example**

```
1%27%20||%201=1%20--+
```

**Encoded Characters**

- `%27` → `'`
- `%20` → space
- `%3D` → `=`
- `%2D%2D` → `-`

### Detection / Mitigation

- Decode URL encoded input before applying security checks.
- Implement prepared statements.
- Log and inspect encoded HTTP requests.

---

# Hexadecimal Encoding SQL Injection

**Definition**

- SQL values are represented using **hexadecimal notation**, allowing attackers to bypass filters that only detect plain text input.

### How It Works

- Strings are converted to hexadecimal format.
- The database interprets the hexadecimal value as a string.
- Filters that search for specific text patterns fail to detect the payload.

### Commands / Payloads

```sql
SELECT * FROM users WHERE name = 0x61646d696e;
```

**Decoded Value**

```
0x61646d696e = admin
```

### Detection / Mitigation

- Monitor SQL queries containing hexadecimal literals.
- Implement prepared statements.
- Apply strict query parameter validation.

---

# Unicode Encoding SQL Injection

**Definition**

- Characters are encoded using **Unicode escape sequences** to bypass ASCII-based input filtering.

### How It Works

- Characters are replaced with Unicode representations.
- Filters that check only ASCII patterns fail to recognise the encoded payload.
- The database interprets the encoded characters as the intended SQL command.

### Commands / Payloads

```
\u0061\u0064\u006d\u0069\u006e
```

**Decoded Value**

```
admin
```

### Detection / Mitigation

- Normalize Unicode input before validation.
- Apply strict server-side input validation.
- Use parameterized queries.

---

# SQL Operator Substitution for Filter Evasion

**Definition**

- Alternative SQL operators are used to bypass filters that block common keywords such as `OR`.

### How It Works

- Instead of using `OR`, attackers use alternative logical operators supported by SQL.
- Filters removing specific keywords may fail to detect these alternatives.

### Commands / Payloads

```sql
1' || 1=1 --
```

### Detection / Mitigation

- Avoid keyword-based filtering approaches.
- Implement parameterized queries.
- Use WAF rules capable of detecting logical operator abuse.

---

## No-Quote SQL Injection

**Definition**

- A SQL injection technique used when applications **filter, escape, or block single (`'`) and double (`"`) quotes**.
- Attackers craft payloads that **avoid quotes entirely** while still manipulating SQL query logic.

### How It Works

- The application blocks or escapes quotation marks.
- Attackers construct payloads using:
    - Numeric conditions
    - SQL comments
    - SQL functions
- The injected payload modifies the SQL logic without needing string delimiters.

### Commands / Payloads

**Numeric Condition Injection**

```sql
OR 1=1
```

**Comment-Based Injection**

```sql
admin--
```

**String Construction Using CONCAT**

```sql
CONCAT(0x61,0x64,0x6d,0x69,0x6e)
```

**Decoded Result**

```
admin
```

### Detection / Mitigation

- Use **prepared statements with parameter binding**.
- Validate expected input types (e.g., enforce numeric-only values).
- Detect abnormal logical conditions such as `1=1`.

---

# SQL Injection Without Spaces

**Definition**

- A filter evasion technique used when applications **remove or block spaces in SQL input**.

### How It Works

- Many filters remove `%20` or literal spaces.
- Attackers replace spaces using:
    - SQL comments
    - Tabs or newline characters
    - Encoded whitespace characters
- The SQL parser interprets these characters as valid spacing.

### Detection / Mitigation

- Normalize whitespace before validation.
- Detect encoded or unusual whitespace characters.
- Use parameterized queries.

---

# Using SQL Comments to Replace Spaces

**Definition**

- SQL comments (`/**/`) can be inserted between SQL keywords to **replace spaces and bypass filters**.

### How It Works

- Filters remove literal spaces.
- Attackers insert comment blocks between SQL keywords.
- The SQL engine ignores comments while parsing the query.

### Commands / Payloads

```sql
SELECT/**/*FROM/**/users/**/WHERE/**/name='admin'
```

### Detection / Mitigation

- Detect unusual SQL comment patterns in input.
- Normalize queries before validation.
- Apply strict input validation.

---

# Using Alternate Whitespace Characters

**Definition**

- Alternate whitespace characters can replace spaces when filters block them.

### How It Works

- SQL interprets certain encoded characters as whitespace.
- Attackers replace spaces with encoded tab or newline characters.
- The database parses them as valid query separators.

### Commands / Payloads

**Tab Character Injection**

```sql
SELECT\t*\tFROM\tusers\tWHERE\tname='admin'
```

**Encoded Whitespace Characters**

```
%09  → Horizontal tab
%0A  → Line feed (newline)
%0C  → Form feed
%0D  → Carriage return
%A0  → Non-breaking space
```

### Detection / Mitigation

- Decode encoded whitespace before processing.
- Monitor abnormal URL-encoded input patterns.
- Enforce strict server-side validation.

---

# SQL Injection Bypass Using Encoded Whitespace

**Definition**

- Encoded newline or tab characters can bypass filters that remove standard spaces.

### How It Works

- Spaces are filtered out by the application.
- Attackers replace spaces with encoded newline characters.
- The SQL parser interprets these as valid spacing.

### Commands / Payloads

**Original Payload**

```sql
1' OR 1=1 --
```

**Whitespace-Bypass Payload**

```
1'%0A||%0A1=1%0A--%27+
```

### Detection / Mitigation

- Normalize encoded input before validation.
- Detect repeated encoded whitespace sequences.
- Implement prepared statements.

---

# SQL Keyword Obfuscation

**Definition**

- SQL keywords can be disguised using **case changes, comments, or encoding** to bypass filtering mechanisms.

### How It Works

- Filters look for exact keyword matches.
- Attackers alter keyword appearance while preserving SQL meaning.

### Commands / Payloads

**Case Manipulation**

```sql
SElEcT * FrOm users
```

**Comment Obfuscation**

```sql
SE/**/LECT * FROM/**/users
```

### Detection / Mitigation

- Normalize SQL keywords before validation.
- Detect fragmented SQL keywords.
- Apply strict query parameterization.

---

# SQL Injection Logical Operator Bypass

**Definition**

- Logical operators such as `OR` and `AND` can be replaced with alternative operators when filters block them.

### How It Works

- Filters remove common logical operators.
- Attackers use alternative operators supported by SQL engines.

### Commands / Payloads

```sql
username='admin' && password='password'
```

```sql
username='admin'/**/||/**/1=1--
```

### Detection / Mitigation

- Detect abnormal logical expressions.
- Restrict query construction using prepared statements.
- Monitor for logical bypass attempts.

---