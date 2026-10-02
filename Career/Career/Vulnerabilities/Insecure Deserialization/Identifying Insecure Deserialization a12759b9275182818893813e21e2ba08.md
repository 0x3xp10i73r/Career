# Identifying Insecure Deserialization

## How to Identify Insecure Deserialization

- Look at **all data passed into the website** and identify anything that looks serialized
- Works for both **whitebox** (source code access) and **blackbox** testing

---

### PHP Serialization Format

- Human-readable string format — letters = data type, numbers = length
- Example object:

```
O:4:"User":2:{s:4:"name":s:6:"carlos";s:10:"isLoggedIn";b:1;}
```

- How to read it:
    - `O:4:"User"` → Object, class name is "User" (4 chars)
    - `2` → has 2 attributes
    - `s:4:"name"` → string key, 4 chars
    - `s:6:"carlos"` → string value, 6 chars
    - `b:1` → boolean true
- Key methods to look for in source code: **`serialize()`** and **`unserialize()`**
- Focus on anywhere **`unserialize()`** is called

---

### Java Serialization Format

- Uses **binary format** — harder to read but still identifiable
- Serialized Java objects always start with the same bytes:
    - Hex: `ac ed`
    - Base64: `rO0`
- Any class implementing **`java.io.Serializable`** can be serialized
- In source code, look for **`readObject()`** — used to deserialize from an InputStream

---

## Manipulating Serialized Objects

- Two approaches:
    - **Edit the byte stream directly**
    - **Write a script** in the target language to create and serialize a new object ← easier for binary formats

---

### Modifying Object Attributes — Example

- Attacker finds this serialized object in a cookie:

```
O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}
```

- `isAdmin` is set to `b:0` (false)
- Attacker changes it to `b:1` (true), re-encodes, and replaces the cookie
- If the server-side code does this:

```php
$user = unserialize($_COOKIE);
if ($user->isAdmin === true) {
    // allow access to admin interface
}
```

- The app blindly trusts the cookie → **instant privilege escalation**
- The serialized object's **authenticity is never verified**

---

### Key Takeaway

- As long as the attacker keeps the **serialized format valid**, the server will deserialize it without question
- Modifying attributes is just the **first step** — the real attack surface goes much deeper