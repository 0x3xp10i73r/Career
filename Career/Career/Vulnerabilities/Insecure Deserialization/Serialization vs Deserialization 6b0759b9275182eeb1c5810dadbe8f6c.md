# Serialization vs Deserialization

# 🔐 Insecure Deserialization

## What is Serialization?

- **Serialization** = converting complex data (objects, fields) into a flat byte stream
- Makes it easier to:
    - Write data to memory, files, or databases
    - Send data over a network or between app components (e.g. API calls)
- The object's **state is preserved** — all attributes and their values are saved

---

## What is Deserialization?

- **Deserialization** = the reverse — restoring a byte stream back into a fully functional object
- The restored object is in the **exact same state** as when it was serialized
- The app then interacts with it like any normal object

---

## Key Facts to Remember

- Different languages serialize differently:
    - Some use **binary formats**
    - Others use **string formats** (varying readability)
- **All attributes are stored** in the serialized data — including **private fields**
- To exclude a field from serialization, mark it as **`transient`** in the class declaration

---

## Language-Specific Terminology

| Language | Term Used |
| --- | --- |
| Java / General | Serialization |
| Ruby | Marshalling |
| Python | Pickling |

> All mean the same thing — just different names per language.
> 

---

## Why Does This Matter in Security?

- Insecure deserialization can lead to **high-severity attacks**
- Common in **PHP, Ruby, and Java** applications
- Attackers can manipulate serialized data to:
    - Tamper with object attributes
    - Achieve **Remote Code Execution (RCE)**
    - Bypass authentication or authorization

---

## Quick Mental Model

```
Object → [Serialize] → Byte Stream → [Send/Store] → [Deserialize] → Object (restored)
```

If the deserialization step **doesn't validate** the incoming data → **vulnerability**