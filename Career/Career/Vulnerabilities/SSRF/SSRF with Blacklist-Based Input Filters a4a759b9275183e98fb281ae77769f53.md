# SSRF with Blacklist-Based Input Filters

# Circumventing Common SSRF Defenses

Many applications implement blacklist filters to block SSRF attempts, typically targeting:

- Specific hostnames (`localhost`, `127.0.0.1`)
- Sensitive URLs (`/admin`, `/internal`)
- Private IP address ranges

## Bypass Techniques for Blacklist Filters

### 1. Alternative IP Representations

Use different representations of loopback addresses:

- **Decimal representation**: `2130706433` (decimal equivalent of `127.0.0.1`)
- **Octal representation**: `017700000001`
- **Abbreviated IPv4**: `127.1` (instead of `127.0.0.1`)
- **IPv6 loopback**: `::1` or `0:0:0:0:0:0:0:1`
- **IPv4-mapped IPv6**: `::ffff:127.0.0.1` or `::ffff:7f00:1`

### 2. Domain Name Manipulation

- Register a domain that resolves to `127.0.0.1`
- Use Burp Collaborator domains like `spoofed.burpcollaborator.net` which resolve to loopback
- Leverage DNS rebinding techniques

### 3. String Obfuscation

- **URL encoding**: Encode blocked characters
    - `localhost` → `%6c%6f%63%61%6c%68%6f%73%74`
    - `127.0.0.1` → `%31%32%37%2e%30%2e%30%2e%31`
- **Double URL encoding**: Apply encoding twice
- **Mixed case**: `LOCALHOST`, `LocalHost`, `lOcAlHoSt`
- **Adding superfluous characters**: `127.0.0.1.example.com` (may resolve to `127.0.0.1` depending on DNS configuration)
- **Using alternative delimiters**: `127.0.0.1:80@evil.com`, `127.0.0.1#.evil.com`

### 4. Redirect-Based Bypasses

Host a controlled server that redirects to the target:

- Provide a URL you control that returns a 3xx redirect to the blocked target
- **Protocol switching during redirect**: HTTP → HTTPS or vice versa
- **Different redirect status codes**: 301, 302, 307, 308
- **Meta refresh redirects**: HTML pages with automatic redirects

### 5. Alternative Hostname References

- Use `0.0.0.0` (may behave similarly to localhost in some contexts)
- Use `localhost.localdomain` or other local aliases
- Leverage internal DNS names that resolve to internal addresses

### 6. Bypassing URL Parsing Discrepancies

Exploit differences between:

- Application URL parser vs. backend HTTP library parser
- URL normalization inconsistencies
- Handling of URL-encoded characters in different components

## Example Bypass Scenarios

**Scenario 1**: Application blocks `127.0.0.1`

- **Bypass**: Use `2130706433` or `017700000001`

**Scenario 2**: Application blocks `localhost`

- **Bypass**: Use `LOCALHOST` or `%6c%6f%63%61%6c%68%6f%73%74`

**Scenario 3**: Application blocks both `127.0.0.1` and alternative representations

- **Bypass**: Use a redirect from your controlled domain: `http://attacker.com/redirect` → `http://127.0.0.1/admin`

## Defense Implications

- Blacklists are inherently fragile and prone to bypass
- Effective SSRF prevention requires a combination of:
    - Strict allowlisting of permitted destinations
    - Proper URL parsing and validation
    - Network-level restrictions on outbound connections
    - Monitoring for anomalous request patterns

## Testing Methodology

1. Identify URL parameters vulnerable to SSRF
2. Test with various bypass techniques systematically
3. Use Burp Suite's Intruder with payload lists for common bypasses
4. Monitor for differences in application behavior or error messages
5. Test redirect-based approaches when direct bypasses fail