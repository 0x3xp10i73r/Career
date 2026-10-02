# What is Authentication

### **1. Authentication Definition**

- **Process**: Verifying identity of user/client
- **Need**: Essential for web security (internet exposure)
- **Factors**: Knowledge, possession, inherence

### **2. Authentication vs Authorization**

- **Authentication**: "Who are you?" (identity verification)
    - Example: Is "Carlos123" really Carlos?
    - Verifies user claims match actual identity
- **Authorization**: "What can you do?" (permissions/access)
    - Example: Can Carlos delete accounts? View other users' data?
    - Determines allowed actions after authentication

### **3. Authentication Vulnerabilities - Two Main Causes**

### **Cause 1: Weak Mechanisms**

- **Issue**: Inadequate protection against brute-force attacks
- **Examples**: No rate limiting, weak passwords allowed

### **Cause 2: Logic Flaws/Implementation Bugs**

- **Issue**: Authentication can be **bypassed entirely**
- **Term**: **"Broken authentication"**
- **Cause**: Poor coding, logical errors in implementation

### **4. Impact of Vulnerable Authentication**

### **Severity Can Be Extreme**

- **Account compromise**: Access to victim's data and functions
- **Privilege escalation**: From low to high privilege accounts

### **High-Privilege Account Compromise**

- **Administrator accounts**: Full application control
- **Potential**: Access internal infrastructure

### **Low-Privilege Account Impact**

- **Data access**: Commercial/business information
- **Attack surface expansion**: Access to internal pages
- **Escalation opportunity**: More attack vectors from inside

---

### **Key Relationships**:

1. **Authentication → Authorization**: First verify identity, then determine permissions
2. **Weakness → Impact**: Flaws lead to varying levels of compromise
3. **Every Account Matters**: Even low-privilege accounts provide attack foothold

### **Critical Insight**:

**"Broken authentication"** = Logic flaws allowing **complete bypass** of authentication, not just weak protection