# A Little about Oauth

**OAuth 2.0 Authentication Vulnerabilities**

**What is OAuth?**

- Authorization framework for third-party access to user accounts
- Users grant limited access without sharing credentials
- Used for: Social logins, API access, data sharing
- Current standard: OAuth 2.0 (different from legacy 1.0)

---

**Key Parties in OAuth Flow:**

1. **Client Application**
    - Website/app wanting user data
    - Example: New app requesting Google contacts
2. **Resource Owner**
    - The **user** whose data is accessed
3. **OAuth Service Provider**
    - Owns user data (Google, Facebook, GitHub)
    - Provides **authorization server** (login/consent)
    - Provides **resource server** (API for data)

---

**Common OAuth Flows (Grant Types):**

**1. Authorization Code (Most Secure)**

- Server-side exchange
- Access token never exposed to browser

**2. Implicit (Legacy, Less Secure)**

- Designed for client-side apps
- Access token returned directly to browser

---

**Basic OAuth Process:**

1. **Request Access** → Client asks for specific permissions
2. **User Consent** → User logs into OAuth provider & approves
3. **Token Issuance** → Client receives access token
4. **API Access** → Client uses token to fetch user data

---

**Why OAuth is Vulnerable:**

- **Complex implementation** → Many mistakes
- **Multiple components** → More attack surface
- **Third-party trust** → Security depends on all parties
- **Common misconfigurations** → Predictable flaws

---

**Key Attack Areas:**

- **Authorization server misconfigurations**
- **Token leakage/theft**
- **Redirect URI validation flaws**
- **CSRF in OAuth flows**
- **Scope manipulation**

---

**OAuth vs. Authentication:**

- **OAuth purpose:** Authorization (data access)
- **Commonly used for:** Authentication (social login)
- **OAuth for auth =** Using access token to identify user
- **Vulnerable when:** Misconfigured or poorly implemented

---