# Hidden Multi-Step Sequences in Race Conditions

## What are Hidden Multi-Step Sequences?

- A **single HTTP request** can trigger **multiple backend steps**
- Application temporarily enters **hidden intermediate states (sub-states)**
- These states:
    - Exist only for milliseconds
    - Are not visible to the user
    - Can be exploited using race conditions

---

## Why They Are Dangerous

- Enable vulnerabilities **beyond limit overruns**
- Can bypass:
    - Authentication
    - MFA
    - Authorization
    - Workflow validation
    - Payment verification

---

## Example: MFA Bypass via Race Condition

Backend flow:

- Session is created
- User is marked as logged in
- MFA enforcement flag is set
- MFA code is generated
- User is redirected

Temporary sub-state:

- User is **logged in**
- MFA **not yet enforced**

Attack:

- Send login request
- Simultaneously send request to sensitive authenticated endpoint
- Access granted before MFA enforcement

---

# Methodology: Predict – Probe – Prove

(From PortSwigger research: *Smashing the State Machine*)

---

## 1️⃣ Predict Potential Collisions

Focus testing on endpoints that:

- Are **security-critical**
- Modify **shared data**
- Update **same database records**

### Ask:

- Does this endpoint affect authentication, payments, balances, roles, orders?
- Can two requests modify the **same record**?

Example:

- Password reset per user → low collision
- Password reset updating global token table → high collision

---

## 2️⃣ Probe for Clues

### Step A – Benchmark normal behavior

- Use Burp Repeater
- Send requests:
    - `Send group in sequence (separate connections)`
- Record:
    - Responses
    - Timing
    - Emails
    - State changes

### Step B – Trigger race condition

- Send same requests:
    - `Send group in parallel`
    - Or Turbo Intruder (single-packet)

### Look for:

- Different responses
- Unexpected success/failure
- Different emails
- Account state changes
- Order/payment inconsistencies
- Token/session anomalies

---

## 3️⃣ Prove the Concept

- Remove unnecessary requests
- Identify minimum working race
- Reproduce consistently
- Understand backend state changes

Mindset:

> Race conditions are structural weaknesses, not single bugs.
> 

---

# Multi-Endpoint Race Conditions

## What are they?

- Race conditions involving **different endpoints**
- Requests interact with same workflow or shared state

---

## Example: Shopping Cart Logic Flaw

Classic bug:

- Add item → Pay → Add more items → Confirm order

Race variation:

- Payment validation happens
- Order confirmation pending
- Add items during this window
- Extra items get confirmed without payment

---

# Aligning Multi-Endpoint Race Windows

Even with single-packet attacks, timing may fail due to:

---

## Causes

### 1. Network architecture delays

- Frontend → backend connection setup
- Protocol differences (HTTP/1 vs HTTP/2)

### 2. Endpoint-specific processing delays

- Some endpoints:
    - Call external APIs
    - Perform heavy DB operations
    - Run async jobs

---

# Workaround: Connection Warming

## Purpose

- Separate **network delays** from **endpoint logic delays**

---

## How to Warm the Connection

In Burp Repeater:

- Add a harmless request first (e.g., `GET /`)
- Group requests
- Send using:
    - `Send group in sequence (single connection)`

---