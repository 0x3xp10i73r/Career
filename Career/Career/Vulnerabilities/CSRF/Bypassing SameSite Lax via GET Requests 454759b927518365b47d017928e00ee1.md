# Bypassing SameSite Lax via GET Requests

**Core Vulnerability:**

- **SameSite=Lax** allows cookies in cross-site **GET** requests with **top-level navigation**
- Many applications accept **state-changing operations via GET** (poor design)
- **Framework method overrides** can convert POST→GET bypassing Lax

---

**Attack Methods:**

**1. Direct GET Endpoints:**

```html
<!-- User clicks link -->
<a href="<https://bank.com/transfer?to=attacker&amount=1000>">
  Free Money!
</a>

<!-- Auto-execute -->
<script>
  document.location = '<https://bank.com/delete-account>';
</script>

```

**2. Framework Method Override:**

- **Symfony/Rails/Laravel:** `_method=GET` parameter
- **Express.js:** `method-override` middleware
- Converts POST to GET server-side

```html
<form method="POST" action="/transfer">
  <input type="hidden" name="_method" value="GET">
  <input type="hidden" name="to" value="attacker">
</form>

```

---

**Testing Approach:**

1. **Identify POST endpoints** that modify state
2. **Change POST → GET**, move params to query string
3. **Test if action executes** without CSRF token
4. **Check for method override** parameters
5. **Verify Lax allows** (top-level navigation required)

---

**Key Insight:**
SameSite=Lax **only blocks POST-based CSRF** - GET-based attacks remain fully possible.