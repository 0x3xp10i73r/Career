# Detecting Blind Command Injection

## Blind OS Command Injection

- The app **does not return command output** in the HTTP response
- Standard `echo` payloads won't work — different techniques needed
- Still fully exploitable, just requires more care

---

### Example Scenario

- A feedback form calls the `mail` program server-side:

```bash
mail -s "This site is great" -aFrom:peter@normal-user.net feedback@vulnerable-website.com
```

- Output is never returned to the user → **blind injection**

---

## Technique 1 — Time Delays

- Inject a command that causes a **measurable delay** in the response
- If the response is slow, the command executed — confirms the vulnerability
- Best tool for this: **`ping`** (lets you control how long it runs)

```bash
& ping -c 10 127.0.0.1 &
```

- This pings the loopback adapter 10 times → causes roughly a **10 second delay**
- If the response takes ~10 seconds longer than usual → **blind injection confirmed**

---

### Why `ping` Works Well

- Available on both **Linux and Windows**
- `c` flag controls the number of packets = controls the delay duration
- No outbound network needed — pings **localhost (`127.0.0.1`)** so it always works
- Clean, reliable, low noise