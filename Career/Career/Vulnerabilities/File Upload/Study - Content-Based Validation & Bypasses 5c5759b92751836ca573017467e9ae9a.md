# Study - Content-Based Validation & Bypasses

### **1. Content Verification Methods**

- **What servers check**: Actual file content, not just headers
- **Image-specific checks**:
    - **Dimensions verification** - PHP scripts have no dimensions
    - **Magic bytes/signatures** - Specific byte sequences

### **2. File Signature Validation**

- **Concept**: Check file header/footer bytes
- **Examples**:
    - **JPEG**: Starts with `FF D8 FF`
    - **PNG**: Starts with `89 50 4E 47`
    - **GIF**: Starts with `GIF89a` or `GIF87a`
- **Purpose**: Verify file type matches expected format

### **3. Polyglot File Attack**

- **Definition**: File that's valid as multiple formats
- **Method**: Embed malicious code in file **metadata/comments**
- **Tool**: **ExifTool** to inject code into image metadata
- **Example**: JPEG file with PHP code in EXIF data

### **4. Bypass Technique**

- **Step 1**: Create polyglot file
    
    ```bash
    exiftool -Comment='<?php system($_GET["cmd"]); ?>' image.jpg
    
    ```
    
- **Step 2**: Rename to `.php` or use extension bypass
- **Result**: File passes as valid image but contains executable code

### **5. Why Content Checks Fail**

- **Metadata injection**: Code in EXIF, comments, etc.
- **File remains valid**: Still has correct dimensions/signatures
- **Server executes metadata**: If parsed as code

---

### **Key Insight**:

Even **content-based validation** can be bypassed by creating **polyglot files** that:

1. **Pass validation** (correct signatures/dimensions)
2. **Contain malicious code** in metadata
3. **Get executed** when interpreted

### **Limitation**:

Content checks are **better than header checks** but still **not foolproof** against polyglot attacks