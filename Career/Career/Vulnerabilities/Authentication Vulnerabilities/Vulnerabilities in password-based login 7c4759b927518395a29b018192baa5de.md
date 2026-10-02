# Vulnerabilities in password-based login

# **Password-Based Login Vulnerabilities (From Your Text)**

### **1. Password-Based Authentication**

- **Method**: Username + secret password
- **Assumption**: Knowing password = proof of identity
- **Vulnerability**: Attacker obtains/guesses credentials

### **2. Brute-Force Attacks**

- **Definition**: Systematic trial-and-error credential guessing
- **Automation**: Uses wordlists of usernames/passwords
- **Tools**: Dedicated tools for high-speed attempts
- **Risk**: High if no brute-force protection

### **3. Username Brute-Forcing**

- **Predictable patterns**:
    - Email format: `firstname.lastname@company.com`
    - Common names: `admin`, `administrator`, `root`
- **Username discovery**:
    - Public user profiles (names may match usernames)
    - HTTP responses (may leak admin emails)
    - Registration forms (reveal taken usernames)

### **4. Password Brute-Forcing**

- **Difficulty depends on**: Password strength/policy
- **Common password policy**:
    - Minimum length
    - Mixed case letters
    - Special characters
- **User behavior weaknesses**:
    - Simple transformations: `mypassword` → `Mypassword1!`
    - Pattern-based: `Mypassword1!` → `Mypassword2?`
    - Predictable modifications for password changes

### **5. Username Enumeration**

- **Definition**: Identifying valid usernames through behavior analysis
- **Common locations**:
    - Login page (valid username + wrong password)
    - Registration forms (username already taken)

### **6. Enumeration Detection Methods**

### **Status Code Differences**

- **Pattern**: Different HTTP status for valid vs invalid usernames
- **Ideal**: Same status code for all outcomes (rarely followed)

### **Error Message Variations**

- **Example**:
    - "Invalid username or password" (both wrong)
    - "Invalid password" (username valid, password wrong)
- **Even subtle differences** (typos, extra spaces) reveal validity

### **Response Time Analysis**

- **Indicator**: Slightly longer response for valid usernames
- **Why**: Extra step to check password only if username valid
- **Amplification**: Enter very long password to increase timing difference

---

### **Key Attack Strategy**:

1. **Enumerate valid usernames** (through behavior differences)
2. **Reduce attack space** (shorter valid username list)
3. **Brute-force passwords** for known valid usernames

### **Human Behavior Exploitation**:

Users **transform simple passwords** to meet policy requirements, creating **predictable patterns** that make brute-forcing more efficient