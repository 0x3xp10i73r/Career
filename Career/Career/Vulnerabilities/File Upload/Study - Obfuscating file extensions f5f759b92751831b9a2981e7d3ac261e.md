# Study - Obfuscating file extensions

# **File Extension Obfuscation Techniques (From Your Text)**

### **1. Case Sensitivity Exploit**

- **Technique**: Use mixed case file extensions
- **Example**: `exploit.pHp`
- **Why it works**: Validation may be **case-sensitive**, but server execution is **case-insensitive**

### **2. Multiple Extensions**

- **Technique**: Add extra extensions after the real one
- **Example**: `exploit.php.jpg`
- **Why it works**: Different parsing algorithms may interpret this as either PHP or JPG

### **3. Trailing Characters**

- **Technique**: Append special characters after extension
- **Examples**:
    - `exploit.php.` (trailing dot)
    - `exploit.php` (trailing space)
- **Why it works**: Some components strip/ignore trailing characters during validation

### **4. URL Encoding Bypass**

- **Technique**: Encode special characters
- **Example**: `exploit%2Ephp`
- **Why it works**: Validation may not decode it, but server does decode it

### **5. Semicolon/Null Byte Injection**

- **Technique**: Insert termination characters
- **Examples**:
    - `exploit.asp;.jpg`
    - `exploit.asp%00.jpg`
- **Why it works**: High-level languages vs low-level languages handle these differently

### **6. Unicode Character Exploitation**

- **Technique**: Use multibyte Unicode characters
- **Examples**: `xC0 x2E`, `xC4 xAE`, `xC0 xAE`
- **Why it works**: Unicode-to-ASCII conversion changes character interpretation

### **7. Recursive Stripping Bypass**

- **Technique**: Embed blacklisted string to survive removal
- **Example**: `exploit.p.phphp`
- **Process**: Stripping `.php` from `exploit.p.phphp` leaves `exploit.p.php`
- **Why it works**: Non-recursive removal leaves dangerous extension intact

---