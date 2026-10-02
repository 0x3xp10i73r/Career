# Modifying Serialized DataTypes

## Modifying Data Types in Serialized Objects

- Beyond changing values, attackers can also **supply unexpected data types**
- PHP is especially vulnerable due to its **loose comparison operator (`==`)**

---

### PHP Loose Comparison — How It Works

| Comparison | Result | Why |
| --- | --- | --- |
| `5 == "5"` | `true` | String converted to integer |
| `5 == "5 of something"` | `true` | String starting with number → PHP uses only the number |
| `0 == "Example string"` | `true` *(PHP 7.x and below only)* | String with no leading number → treated as `0` |
| `0 == "Example string"` | `false` *(PHP 8+)* | Strings no longer implicitly cast to `0` |

---

### Real Attack Example — Authentication Bypass

- Vulnerable code:

```php
$login = unserialize($_COOKIE);
if ($login['password'] == $password) {
    // log in successfully
}
```

- Attacker changes the `password` attribute in the serialized object from a string to **integer `0`**
- If the real stored password **doesn't start with a number**, PHP evaluates `0 == "somepassword"` as `true`
- Result: **authentication bypass — no password needed**

> 💡 This only works because deserialization **preserves data types**. If the value came from a raw request parameter, `0` would be cast to a string and the comparison would fail.
> 

---

### ⚠️ Important Rule When Modifying Data Types

- Always update the **type labels and length indicators** in the serialized string
- Example: changing `s:6:"carlos"` to an integer means updating `s:` → `i:` and removing the length/quotes
- If you don't, the object becomes **corrupted and won't deserialize**

---

### Version Note

- **PHP 8+**: `0 == "string"` now returns `false` — this specific bypass no longer works
- **PHP 8+**: `5 == "5 of something"` still returns `true` — alphanumeric string behavior unchanged