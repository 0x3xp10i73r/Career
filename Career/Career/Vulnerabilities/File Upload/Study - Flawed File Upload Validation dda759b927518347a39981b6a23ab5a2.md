# Study - Flawed File Upload Validation

### **1. Multipart Form Data Structure**

- **Content-Type**: `multipart/form-data` for file uploads
- **Format**: Message body split into **separate parts** for each form field
- **Each part contains**:
    - `Content-Disposition` header (field name, filename)
    - Optional `Content-Type` header (MIME type of data)
    - Data/content

### **2. Flawed MIME Type Validation**

- **What servers check**: Only the `Content-Type` header in the part
- **Expected types**: `image/jpeg`, `image/png`, etc.
- **Problem**: **Implicit trust** in client-provided `Content-Type`
- **No verification**: Doesn't check if file **content actually matches** the claimed type

### **3. Bypass Technique**

- **Attack**: Change `Content-Type` header while keeping malicious content
- **Example**:
    
    ```
    Content-Disposition: form-data; name="image"; filename="shell.php"
    Content-Type: image/jpeg  # ← Lies about file type
    
    <?php system($_GET['cmd']); ?>  # ← Actual PHP content
    
    ```
    
- **Result**: Server thinks it's an image, but executes as PHP

### **4. Exploitation Tools**

- **Primary tool**: **Burp Repeater**
- **Method**: Intercept request → Modify `Content-Type` → Send
- **Simple bypass**: Change from `application/x-php` to `image/jpeg`

---

### **Key Vulnerability**:

Server validates **only the header**, not the **actual file content**

### **Defense Gap**:

Trusting **client-controlled** `Content-Type` header without **content verification**