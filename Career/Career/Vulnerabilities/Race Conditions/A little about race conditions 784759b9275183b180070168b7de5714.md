# A little about race conditions

## Race Conditions – Security Notes

### What is a Race Condition?

- A vulnerability that occurs when:
    - An application processes multiple requests at the same time
    - And shared data is accessed/modified without proper locking or safeguards
- Results in:
    - Unexpected behavior
    - Business logic bypass
    - Financial or data integrity issues

---

### Race Window

- The short time period when:
    - The application is in an incomplete or temporary state
    - And data has not yet been fully updated
- Even a few milliseconds is enough for exploitation

---

### How Race Condition Attacks Work

- Attacker sends multiple identical requests simultaneously
- Requests are processed in parallel
- All requests pass validation before the database is updated
- Leads to:
    - Multiple successful actions instead of one

---

### Example: Reusing a One-Time Discount Code

Normal flow:

- Check if code already used
- Apply discount
- Mark code as used in database

Vulnerable flow:

- Two requests arrive at the same time
- Both pass the "not used yet" check
- Both apply the discount
- Database updated afterward

Result:

- Same code used **multiple times**

---

### Common Limit Overrun Scenarios

Race conditions can allow:

- Redeeming a gift card multiple times
- Using a discount code more than once
- Rating a product repeatedly
- Bypassing CAPTCHA reuse protection
- Bypassing login rate limits
- Withdrawing more money than available balance
- Performing duplicate refunds or transfers

---

### Limit Overrun Race Conditions (TOCTOU)

- Stands for: **Time Of Check → Time Of Use**
- Happens when:
    - Validation and execution are **separate steps**
    - No locking exists between them
- This creates the race window

---

### Why Race Conditions Are Dangerous

- Break business logic
- Cause financial loss
- Allow fraud
- Corrupt application state
- Often difficult to detect with normal testing

---