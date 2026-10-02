# Study - File Execution Prevention & Bypasses

### **1. MIME Type Execution Control**

- **Server behavior**: Only executes files with configured MIME types
- **Result**: Unconfigured files return as **plain text** or error
- **Example**: PHP file served as text instead of executing
- **Impact**: Prevents web shell execution but may **leak source code**

### **2. Directory-Based Security**

- **Different security per directory**:
    - **Upload directories**: Strict execution controls
    - **Other directories**: May allow execution
- **Attack vector**: Upload to **non-upload directories**
- **Method**: Find paths without execution restrictions

### **3. Filename Field Exploitation**

- **Key insight**: `filename` field in multipart requests determines save location
- **Attack**: Manipulate `filename` to save to different directory
- **Example**: `filename="../../../otherdir/shell.php"`
- **Goal**: Save to directory with less restrictions

### **4. Reverse Proxy Configuration Discrepancies**

- **Architecture**: Requests go to **load balancer/reverse proxy** → **backend servers**
- **Risk**: Different servers may have **different configurations**
- **Attack**: Target server with weaker security settings
- **Method**: May require fuzzing different endpoints

### **5. Source Code Disclosure via Misconfiguration**

- **When**: Server serves script files as plain text
- **Impact**: Can **read source code** of uploaded files
- **Use**: Information disclosure for further attacks

---

### **Key Defense Principle**:

Servers prevent execution by:

1. **Not configuring MIME types** for scripts in upload directories
2. **Applying strict controls** only to user-accessible directories
3. **Serving unconfigured files as text** instead of executing

### **Key Attack Principle**:

Bypass by:

1. **Uploading to directories** with execution enabled
2. **Exploiting path traversal** in filename field
3. **Leveraging configuration differences** between servers