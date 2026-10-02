# Redirecting Output to a File

## Technique 2 — Redirecting Output to a File

- If you can't see command output directly, **write it to a file** in the web root and retrieve it via browser
- Requires knowing (or guessing) a **web-accessible directory** on the server

---

### Example

- App serves static files from `/var/www/static`
- Inject:

```bash
& whoami > /var/www/static/whoami.txt &
```

- Then fetch the output in your browser:

```
https://vulnerable-website.com/whoami.txt
```

---

### Key Points

- `>` redirects command output to a file — **overwrites** if file exists
- The file must be written to a **web-accessible path** — otherwise you can't retrieve it
- Common paths to try: `/var/www/static`, `/var/www/html`, `/var/www/public`
- Works for any command — not just `whoami` — useful for reading files, dumping configs, etc.