# OAuth Grant Types

## OAuth Grant Types – Overview

- OAuth grant type defines **how a client gets an access token**
- Also called **OAuth flows**
- Determines:
    - Request sequence
    - How tokens are delivered
    - Security level
- Client must specify the grant type using `response_type`
- OAuth server must support the requested grant type

---

## OAuth Scopes

- Define **what data** and **what actions** the client can access
- Sent using the `scope` parameter in authorization request
- Scope format varies by provider:
    - `contacts`
    - `contacts.read`
    - `https://example.com/scopes/contacts.read`
- For authentication, OpenID Connect scopes are common:
    - `openid`
    - `profile`
    - `email`

---

## Authorization Code Grant Type (Most Secure)

### Key Characteristics

- Uses browser redirects + server-to-server communication
- Access token is **never exposed to the browser**
- Uses `client_secret` for client authentication
- Recommended for **server-side applications**

---

### Flow Steps

### 1. Authorization Request

- Client redirects user to OAuth server
- Includes:
    - `client_id`
    - `redirect_uri`
    - `response_type=code`
    - `scope`
    - `state` (CSRF protection)

---

### 2. User Login and Consent

- User logs in to OAuth provider
- User approves or denies requested scopes
- Consent may be skipped if already approved earlier

---

### 3. Authorization Code Issued

- OAuth server redirects browser to `redirect_uri`
- Sends:
    - `code`
    - `state`

---

### 4. Access Token Request (Back-channel)

- Client server sends POST request to `/token`
- Includes:
    - `client_id`
    - `client_secret`
    - `code`
    - `redirect_uri`
    - `grant_type=authorization_code`

---

### 5. Access Token Granted

- OAuth server returns:
    - `access_token`
    - `token_type`
    - `expires_in`
    - `scope`
    - (optional) `refresh_token`

---

### 6. API Call

- Client calls resource server
- Sends token in header:
    - `Authorization: Bearer <token>`

---

### 7. Resource Grant

- Resource server validates token
- Returns user data based on scope
- Client uses data to authenticate user

---

### Security Advantages

- Tokens not exposed to browser
- Supports client authentication
- Resistant to:
    - Token leakage
    - XSS
    - URL logging attacks

---

## Implicit Grant Type (Less Secure)

### Key Characteristics

- No authorization code step
- Access token returned directly via browser
- No client_secret
- Designed for:
    - Single Page Applications
    - Native apps (legacy usage)
- Now largely replaced by **Authorization Code + PKCE**

---

### Flow Steps

### 1. Authorization Request

- Same as code flow
- Uses:
    - `response_type=token`

---

### 2. User Login and Consent

- Same as authorization code flow

---

### 3. Access Token Granted

- OAuth server redirects to `redirect_uri`
- Token sent in URL fragment:
    
    ```
    #access_token=...
    ```
    
- Client-side JavaScript extracts token

---

### 4. API Call

- Browser sends token to resource server
- Token included in Authorization header

---

### 5. Resource Grant

- Resource server validates token
- Returns user data
- Client logs user in

---

### Security Risks

- Token exposed to:
    - Browser history
    - JavaScript
    - Referrer headers
    - XSS
- No client authentication
- No refresh tokens (usually)

---

## Quick Comparison

| Feature | Authorization Code | Implicit |
| --- | --- | --- |
| Token exposure | Server only | Browser |
| Client authentication | Yes | No |
| Refresh token | Yes | No |
| Security level | High | Low |
| Recommended today | Yes | No |

---

If you want, I can also provide:

- OAuth vulnerability checklist
- Common misconfigurations attackers exploit
- PKCE flow notes
- Real-world bug bounty examples