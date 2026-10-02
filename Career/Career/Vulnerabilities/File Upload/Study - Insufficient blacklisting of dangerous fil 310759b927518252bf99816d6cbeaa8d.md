# Study - Insufficient blacklisting of dangerous file types

Blacklist Bypass Techniques

### **1. Insufficient Blacklisting**

- **Problem**: Blocking only common dangerous extensions
- **Bypass**: Use **lesser-known executable extensions**
- **Examples**: `.php5`, `.shtml`, `.phtml`, `.phps`
- **Why it works**: Blacklist misses alternative executable extensions

### **2. Server Configuration Override**

- **Technique**: Upload server configuration files
- **Files to upload**:
    - **Apache**: `.htaccess` file
    - **IIS**: `web.config` file
- **Purpose**: Change how server handles file types in upload directory

### **3. .htaccess Attack (Apache)**

- **Method**: Upload `.htaccess` with custom directives
- **Example content**:
    
    ```
    AddType application/x-httpd-php .php
    
    ```
    
- **Effect**: Tells Apache to execute `.php` files in that directory

### **4. web.config Attack (IIS)**

- **Method**: Upload `web.config` with custom MIME mapping
- **Example content**:
    
    ```xml
    <staticContent>
      <mimeMap fileExtension=".json" mimeType="application/json" />
    </staticContent>
    
    ```
    
- **Effect**: Can map custom extensions to executable MIME types

### **5. Custom Extension Mapping**

- **Technique**: Use configuration file to map arbitrary extensions to executable types
- **Example**: Make `.myext` execute as PHP via `.htaccess`:
    
    ```
    AddType application/x-httpd-php .myext
    
    ```
    
- **Why it works**: Server trusts uploaded configuration files

---

### **Key Insights**:

1. **Blacklists are incomplete** - they miss alternative executable extensions
2. **Server configuration files** can override global settings
3. **Directory-specific configs** (`.htaccess`, `web.config`) can be weaponized
4. **Custom MIME type mapping** can make any extension executable