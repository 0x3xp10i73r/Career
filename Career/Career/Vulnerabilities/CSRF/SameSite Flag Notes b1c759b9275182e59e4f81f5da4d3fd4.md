# SameSite Flag Notes

**SameSite Cookie Restrictions & Bypasses**

**What is SameSite?**

- Browser security mechanism controlling when cookies are sent in cross-site requests
- Partial protection against CSRF, cross-site leaks, CORS attacks
- **Chrome default:** Lax SameSite if not explicitly set (since 2021)

---

Key Concepts:

Site (for SameSite):

- Definition: TLD + one additional level (TLD+1)
- Example: `example.com`, `app.example.com`, `intranet.example.com` = same site
- Considers: Scheme (HTTP/HTTPS) - `http://` ≠ `https://` for SameSite

Origin vs Site:

- Origin: Exact scheme + domain + port
- Site: Scheme + TLD+1 (broader scope)
- Cross-origin requests can still be same-site

Example: `https://app.example.com` → `https://intranet.example.com`

- ✅ Same-site
- ❌ Different origin

---

SameSite Restriction Levels:

1. Strict:

- ❌ No cross-site cookie sending
- Most secure, poor user experience
- Breaks legitimate cross-site functionality

2. Lax (Chrome default):

- ✅ Cross-site cookies ONLY if:
    - GET method (not POST)
    - **Top-level navigation** (user clicks link)
- ❌ Blocks: POST requests, background requests (scripts, iframes, images)

3. None:

- ✅ Cookies sent in all cross-site requests
- Must include `Secure` attribute (HTTPS only)
- Legacy behavior for some browsers

---

**S**ecurity Implications:

Lax Bypasses Possible:

- CSRF via GET requests with top-level navigation
- User interaction required (link clicks)

Strict More Secure:

- Blocks all cross-site cookie sending
- But can be bypassed in same-site, cross-origin scenarios

None = Dangerous:

- Effectively disables SameSite protection
- Common workaround after Chrome's Lax default
- May expose sensitive cookies

---

Testing Considerations:

1. Check `Set-Cookie` headers for `SameSite` attribute
2. Default behavior depends on browser/version
3. `SameSite=None` requires `Secure` flag
4. Look for cookies without explicit `SameSite` (may default to Lax)

Bypass Potential:

- Same-site, cross-origin attacks still possible
- JavaScript execution on any same-site domain can affect others
- GET-based CSRF with user interaction (Lax restriction)