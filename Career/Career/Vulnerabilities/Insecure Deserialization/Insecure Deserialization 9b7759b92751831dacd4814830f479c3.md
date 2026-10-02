# Insecure Deserialization

## What is Insecure Deserialization?

- Occurs when **user-controllable data** is deserialized by a website
- Attackers can manipulate serialized objects to **pass harmful data** into the app
- Attackers can even **swap in an object of a completely different class**
    - Any class available to the website will be deserialized and instantiated — regardless of what was expected
    - This is why it's also called an **"Object Injection"** vulnerability

---

## How Attacks Play Out

- An unexpected class object might cause an exception — but **damage may already be done**
- Many attacks complete **before deserialization even finishes**
- The deserialization process itself can trigger the attack — even if the app never directly uses the malicious object
- Even **strongly typed languages** are not immune

---

## Impact of Insecure Deserialization

- Massively increases the **attack surface**
- Allows attackers to **reuse existing app code** in harmful ways
- Common outcomes:
    - **Remote Code Execution (RCE)** — most severe
    - **Privilege Escalation**
    - **Arbitrary File Access**
    - **Denial of Service (DoS)**