# OAuth Authentication

## OAuth Authentication – Overview

- OAuth was originally designed for **authorization**, not authentication
- It is now widely used for **user login (SSO)**
- Common example: **“Login with Google / Facebook / GitHub”**
- Provides functionality similar to **SAML-based Single Sign-On (SSO)**

---

## How OAuth is Used for Authentication

- Client application uses OAuth to:
    - Identify the user
    - Create an authenticated session
- User identity is derived from:
    - Email
    - Username
    - User ID
    - Or other profile data

---

## OAuth Authentication Flow (High Level)

1. **User chooses social login**
    - Clicks “Login with social media”
2. **OAuth authorization flow starts**
    - Client redirects user to OAuth provider
    - Requests permission to access identity data (via scopes)
3. **Access token received**
    - Client obtains access token using OAuth flow
4. **User data requested**
    - Client calls resource server `/userinfo` endpoint
    - Sends access token
5. **User authenticated**
    - Client uses returned data as:
        - Username / account identifier
    - Creates a local session for the user

---

## Important Notes

- Access token often acts like a **temporary password**
- Client must trust the OAuth provider
- Security depends on:
    - Correct token validation
    - Proper redirect URI validation
    - Secure user mapping logic

---