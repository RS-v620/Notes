---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠== You can later replace the placeholder Drawing data with a generated diagram (Command Palette: 'Create new drawing').

# Android Mobile OAuth 2.0 Authorization Code + PKCE Flow (Google / Facebook / LinkedIn)

## Actors (Swimlanes)
- Resource Owner (End User)
- Android App (Public OAuth Client)
- System Browser / Custom Tab (User-Agent)
- External Authorization Server (IdP: Google / Facebook / LinkedIn)
- Token Endpoint (part of Authorization Server)
- Your Backend (Optional API Gateway / Resource Server)
- External Resource Server (Provider APIs) *and/or* Your Protected API

## Text Elements (intended for diagram labels)
1) App Registration (Console)
2) Launch Auth Request (PKCE code_challenge)
3) System Browser Presents Login & Consent
4) Authorization Code Redirect (app scheme / https+app link)
5) Code + code_verifier (Token Request)
6) Access Token (and optional Refresh Token)
7) Token Introspection / JWT Validation (kid, iss, aud, exp, nbf)
8) API Call with Bearer Access Token
9) Refresh (Silent) using Refresh Token
10) Logout / Token Revocation
11) Threats & Mitigations (PKCE, App Links, No client_secret)
12) Variation: Backend Exchanges Code (Confidential Proxy Pattern)

> [!note] This file includes a thorough textual breakdown below if you prefer prose over the visual diagram. You can keep BOTH.

## Detailed Step-by-Step Flow
### 0. Prerequisites / Registration
- Developer registers the Android app in each provider's developer console (e.g., Google Cloud Console, Meta for Developers, LinkedIn Developer Portal).
- Provide: App name, package name, SHA-256 signing certificate fingerprint (Android), and authorized redirect URI(s).
- Prefer an HTTPS App Link redirect (e.g., https://auth.example.com/cb) with Digital Asset Links to mitigate interception; fallback is a custom scheme (myapp://oauth2redirect) which is simpler but susceptible to code interception if another app claims the same scheme.
- Result: Obtain a client_id (no client_secret for pure public mobile client). Store metadata locally (not hardcoded secrets). 

### 1. User Initiates Login
- User taps "Sign in with <Provider>". App generates:
  - code_verifier: 43–128 char high-entropy (A-Z a-z 0-9 - . _ ~)
  - code_challenge = BASE64URL-ENCODE(SHA256(ASCII(code_verifier))) (S256 REQUIRED if supported)
- App constructs authorization request:
  response_type=code
  client_id=<client_id>
  redirect_uri=<registered redirect>
  scope=openid profile email (example)
  state=<CSRF token>
  code_challenge=<code_challenge>
  code_challenge_method=S256

### 2. External Authorization (Browser / Custom Tab)
- Open system browser / Chrome Custom Tab (never an embedded WebView) with the authorization URL.
- Provider handles authentication (password, biometric, existing session) and consent.
- Provider returns redirect to registered redirect_uri with code & state.

### 3. Authorization Code Reception
Two variants:
A) Direct (Pure Mobile): App intercepts redirect (custom scheme or App Link) and extracts `code` and validates `state`.
B) Backend-Assisted: Redirect_uri points to your backend; backend immediately exchanges code for tokens then issues a short-lived session / downstream token to the app.

### 4. Token Exchange (Authorization Code Grant w/ PKCE)
Direct (A): App POSTs to token endpoint (over TLS):
  grant_type=authorization_code
  code=<code>
  redirect_uri=<same>
  client_id=<client_id>
  code_verifier=<original code_verifier>
Backend (B): Server sends same request but MAY include a confidential client_secret if it registered one (for a separate confidential web app client). Mobile app then receives filtered tokens (often an app-scoped access token or custom session token) from its own backend.

### 5. Token Response
- Provider returns JSON: access_token, token_type=Bearer, expires_in, optionally refresh_token (Google often with offline access consent), id_token (for OpenID Connect providers like Google).
- Mobile stores tokens in encrypted storage (e.g., EncryptedSharedPreferences / Android Keystore). Avoid plaintext disk or logs.

### 6. Resource Access
- App calls your backend (preferred) OR directly provider APIs with HTTP Authorization: Bearer <access_token>.
- If calling your backend: Backend validates access_token (JWT verification using JWK Set). Checks: signature (kid), iss, aud, exp, nbf, possibly scope/roles. Optionally caches JWK keys.
- If backend needs provider data, it may call provider resource endpoints using the same access_token (if audience allows) or exchange for another token (token exchange pattern) – out of scope here.

### 7. Refresh Flow
- When access_token near expiry (or 401), app uses stored refresh_token:
  grant_type=refresh_token
  refresh_token=<refresh_token>
  client_id=<client_id>
- Provider issues new access_token (+ optionally new refresh_token rotation). Implement rotation detection; discard old token if new refresh_token provided.
- If refresh fails (invalid_grant), force re-auth.

### 8. Logout / Revocation
- Local logout: Delete tokens locally.
- Global revocation: Call provider revocation endpoint (if offered) passing refresh_token or access_token.
- OIDC end_session endpoint for providers that support RP-initiated logout (Google has limited support; LinkedIn / Facebook vary).

### 9. Security Considerations
- PKCE (S256) REQUIRED to mitigate code interception (mobile clients are public).
- Use HTTPS App Link to reduce custom scheme hijack risk.
- Never embed a client_secret in the mobile binary.
- Validate returned state & (if using OIDC) nonce in id_token.
- Enforce minimal scope principle; request only required scopes.
- Pin or monitor JWK fetch; handle key rotation gracefully.
- For backend proxy pattern, treat mobile-issued session tokens separately and sign them with your own key (defense-in-depth).

### 10. Backend vs Direct Comparison
| Aspect | Direct Mobile Exchange | Backend-Assisted Exchange |
|--------|------------------------|---------------------------|
| Token Handling | App stores provider tokens | Backend stores/filters; app gets app token |
| Secret Use | None (public client) | Backend may use client_secret |
| Attack Surface | Interception & storage on device | Adds network hop & complexity |
| Custom Claims | Limited to provider | Backend can mint custom JWT |

### 11. Provider Registration Nuances
- Google: Use Google Cloud Console -> OAuth consent screen -> Credentials -> OAuth Client ID (Android type). Also may need Web App client for backend.
- Facebook: App Dashboard -> Add Android platform; set package, key hash.
- LinkedIn: Developer Portal -> Auth tab; configure redirect URLs & scopes.

### 12. Failure / Edge Cases
- invalid_grant on token request: wrong code_verifier / reused code / expired code.
- invalid_request: mismatched redirect_uri.
- access_denied: user declined consent.
- slow network & user cancels: app should allow retry; do not reuse code_verifier for a new attempt.

### 13. Threat Mitigations Mapping
| Threat | Control |
|--------|---------|
| Code interception | PKCE S256 + HTTPS App Link |
| Token theft (device) | Encrypted storage + OS keychain |
| Replay of refresh_token | Rotation & detect reuse |
| Phishing via embedded webview | Use system browser / custom tab |
| Audience confusion | Validate iss & aud on every call |

## Minimal Sequence (Condensed)
1. Register app (get client_id, set redirect).  
2. Generate code_verifier & code_challenge (S256).  
3. Open authorization URL in system browser.  
4. Receive code (validate state).  
5. Exchange code + code_verifier at token endpoint.  
6. Store & use access_token (and id_token if OIDC).  
7. Call resource server with Bearer token.  
8. Refresh when expiring.  
9. Revoke / logout when needed.  

---
### How to Turn This Into a Visual Diagram
1. In Excalidraw view, create vertical swimlanes for each Actor in the order above.
2. Add numbered arrows (1–12) left-to-right crossing swimlanes to match steps.
3. Place security notes (locks) near PKCE and redirect.
4. Optionally color-code: Registration (grey), Auth (blue), Token (green), Resource calls (purple), Security (red side notes).

> [!tip] You can copy the "Text Elements" list into Excalidraw and convert each to a sticky note or label.

---
## (Optional) Placeholder Drawing Data
The compressed JSON block is intentionally omitted to avoid inserting invalid binary; generate visually in the editor for accuracy.
