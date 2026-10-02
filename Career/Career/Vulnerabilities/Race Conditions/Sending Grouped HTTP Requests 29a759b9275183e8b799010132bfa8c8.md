# Sending Grouped HTTP Requests

## Overview

- Burp Repeater allows sending **multiple HTTP requests together** using *Group send options*.
- Requests can be sent:
    - **In sequence** (one after another)
    - **In parallel** (all at once)
- Useful for:
    - Race condition testing
    - Multi-step workflows
    - Timing-based attacks
    - Request smuggling / desync testing

---

## How to Send Grouped Requests

### Setup

- Create a **Repeater tab group**
- Add relevant request tabs to the group
    
    *(or duplicate tabs to quickly create identical requests for race condition testing)*
    

### Send Options

From the **Send** dropdown:

- Send group in sequence (single connection)
- Send group in sequence (separate connections)
- Send group in parallel

Click **Send group** to execute.

---

## Sending Requests in Sequence

### 1. Single Connection Mode

- One TCP connection is opened
- All requests are sent through it
- Connection is closed afterward

**Benefits:**

- Reduces TCP connection jitter
- Useful for:
    - Client-side desync testing
    - Timing-based attacks
    - Comparing response delays accurately

**Requirements:**

- All requests must:
    - Target same host
    - Use same HTTP version (all HTTP/1 or all HTTP/2)

---

### 2. Separate Connections Mode

- Each request:
    - Uses a new TCP connection
    - Is sent individually in order

**Best for:**

- Multi-step workflows
- Stateful logic testing
- Authentication flows
- Token chaining

---

### Sequence Mode Prerequisites

- No WebSocket tabs in group
- No empty tabs

---

## Sending Requests in Parallel (Race Condition Testing)

### Purpose

- Identify and exploit **race conditions**
- Trigger **limit overrun / TOCTOU** vulnerabilities

### How Burp Synchronizes Requests

### HTTP/1

- Uses **Last-byte synchronization**
- Sends all requests
- Holds last byte
- Releases all final bytes together

### HTTP/2+

- Uses **Single-packet attack**
- Sends all requests inside **one TCP packet**
- Minimizes network jitter

---

### Parallel Mode Features

- Response order indicator:
    - Example: `1/3`, `2/3`, `3/3`
- Ensures all requests arrive **simultaneously**

---

### Parallel Mode Limitations

- Macro requests **cannot** be sent in parallel

---

### Parallel Mode Prerequisites

- All requests must have:
    - Same host
    - Same port
    - Same transport protocol
- HTTP/1 keep-alive **must be disabled** in project settings

---

## Use Case Summary

| Mode | Best Used For |
| --- | --- |
| Sequence (single conn) | Client-side desync, timing attacks |
| Sequence (separate conn) | Multi-step logic testing |
| Parallel | Race condition exploitation |

---