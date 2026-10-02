# Spawning Interactive Shells

## Why This Matters

Landing in a limited/jail shell = can't use `su`, `sudo`, or many commands. Need to upgrade to a full TTY. Python won't always be available — need multiple methods ready.

---

## Shell Spawn Methods (Use Whatever is Available)

### 1. Direct Shell Binary

```bash
/bin/sh -i          # interactive mode flag
/bin/bash -i
```

### 2. Python (most common)

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### 3. Perl

```bash
perl -e 'exec "/bin/sh";'
# Or from inside a script:
perl: exec "/bin/sh";
```

### 4. Ruby

```bash
# From script:
ruby: exec "/bin/sh"
```

### 5. Lua

```bash
# From script:
lua: os.execute('/bin/sh')
```

### 6. AWK

```bash
awk 'BEGIN {system("/bin/sh")}'
```

### 7. Find

```bash
# Using find + awk
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;

# Direct exec (simplest)
find . -exec /bin/sh \; -quit
```

### 8. VIM

```bash
# Launch vim and spawn shell
vim -c ':!/bin/sh'

# Or from inside vim
vim
:set shell=/bin/sh
:shell
```

---

## Quick Reference Table

| Tool | Command | Requires |
| --- | --- | --- |
| sh/bash | `/bin/sh -i` | Always present |
| Python | `python -c 'import pty;pty.spawn("/bin/sh")'` | Python installed |
| Perl | `perl -e 'exec "/bin/sh";'` | Perl installed |
| Ruby | `exec "/bin/sh"` | Ruby installed |
| Lua | `os.execute('/bin/sh')` | Lua installed |
| AWK | `awk 'BEGIN {system("/bin/sh")}'` | AWK (standard on most Unix) |
| Find | `find . -exec /bin/sh \; -quit` | find (standard on all Unix) |
| VIM | `vim -c ':!/bin/sh'` | VIM installed |

> Note: `/bin/sh` can be replaced with any shell binary present on the system (`/bin/bash`, `/bin/zsh`, etc.)
> 

---

## Key Takeaways

- Always have **3-4 shell spawn methods memorized** — you won't know what's available until you're on the box
- `awk` and `find` are nearly universal on Unix/Linux → reliable fallbacks even without Python/Perl
- VIM shell escape = niche but useful when vim is the only interactive tool available
- First thing after spawning a shell: **check `sudo -l`** — may give immediate privilege escalation path
- `NOPASSWD: ALL` in sudo output = run `sudo /bin/bash` → instant root shell