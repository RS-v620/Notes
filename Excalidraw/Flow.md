---
# Android Mobile OAuth 2.0 Authorization Code + PKCE Flow
#public #oauth2 #pkce #design
---

> Comprehensive end-to-end textual blueprint covering BOTH the Android public client and the first-party backend / resource server when delegating authentication to external providers (Google, Facebook, LinkedIn, etc.).

## 1. Core Mental Model
You are outsourcing identity & consent to an external Authorization Server (IdP). Your app obtains delegated tokens, your backend validates or brokers them. See [[Authorization Code Grant]], [[Authorization Endpoint]], [[Token Endpoint]], and [[PCKE-Protocol]].

Actors:
- Resource Owner (End User)
- Android App (Public OAuth Client)
- System Browser / Custom Tab (User-Agent)
- External Authorization Server (Provider)
- Token Endpoint (part of provider)
- Your Backend API (Resource Server / Token Broker)
- External Provider Resource APIs

PKCE (S256) REQUIRED for mobile public clients. No embedded client_secret.

## 2. Registration (One-Time per Provider)
Client:
1. Register Android app (Google Cloud Console, Meta Developers, LinkedIn Developer Portal).
2. Provide: package name, SHA-256 signing cert fingerprint, authorized redirect URIs (prefer HTTPS App Link such as https://auth.example.com/cb; fallback custom scheme myapp://oauth2redirect).
3. Obtain `client_id` (public). Optionally register a confidential Web Client for backend exchanges (has `client_secret`).
4. Persist provider metadata: issuer, authorization endpoint, token endpoint, jwks_uri, scopes.

Server:
1. Maintain registry of provider configs.
2. Map provider scopes -> internal roles.
3. Decide direct vs brokerage strategy.

## 3. Login / Authorization Request
Client steps:
1. User taps Sign In.
2. Generate: `state`, `nonce` (if OIDC), `code_verifier` (43–128 chars per [[PCKE-Protocol]]), `code_challenge = BASE64URL-ENCODE(SHA256(ASCII(code_verifier)))`.
3. Persist transient context (state, nonce, code_verifier, provider, timestamp).
4. Launch system browser (NOT embedded WebView) with:
```
response_type=code
client_id=...
redirect_uri=https://auth.example.com/cb
scope=openid profile email
state=<random>
nonce=<random>
code_challenge=<S256>
code_challenge_method=S256
```
5. Provider handles authentication + consent.

If backend redirect used, backend receives `code` + `state` at /cb and validates `state`.

## 4. Authorization Code Handling Patterns
A. Direct (Mobile Exchange): Redirect returns to app (App Link / custom scheme) → app extracts `code`, validates `state`.
B. Backend-Assisted: Redirect hits backend → backend exchanges code → mints internal tokens → app retrieves them (poll, one-time code, or same redirect flow with ephemeral token).

Tradeoff: Direct simpler; backend-assisted enables token brokerage, claim enrichment, revocation control.

## 5. Token Exchange (Authorization Code + PKCE)
Direct Mobile POST to provider token endpoint:
```
grant_type=authorization_code&code=...&redirect_uri=...&client_id=...&code_verifier=...
```
Backend Confidential POST (if using confidential web client):
```
grant_type=authorization_code&code=...&redirect_uri=...&client_id=WEB_CLIENT&client_secret=...&code_verifier=...
```
Provider Response (typical OIDC): `access_token`, `expires_in`, maybe `refresh_token`, `id_token`, `scope`, `token_type=Bearer`.

Client (Direct): Validate `id_token` (sig via JWK, `iss`, `aud`, `exp`, `nonce`). Securely store tokens (encrypted). Schedule proactive refresh.
Server (Broker): Validate tokens → normalize identity → optionally mint internal short-lived access token + refresh token (internal semantics).

## 6. Using Tokens to Call Your APIs
Scenario 1 (Direct Provider Token): App calls your API with provider `access_token`.
Server validates:
- Signature (JWT) or introspection (opaque)
- `iss`, `aud`, `exp`, `nbf`
- Scope mapping → internal authorization context

Scenario 2 (Brokered Internal Token): App calls API with your internal JWT; backend holds provider access/refresh tokens server-side.

Hybrid: Start direct, migrate to brokerage without changing client PKCE logic.

## 7. Refresh Flow
Direct:
```
grant_type=refresh_token&refresh_token=...&client_id=...
```
Brokered:
`POST /auth/refresh` with internal refresh token → backend (if needed) refreshes provider token → rotates internal tokens.

Handle rotation: replace old refresh token; if reused later → treat as theft signal.

## 8. Logout / Revocation
Layers:
1. Local: delete stored tokens.
2. Provider revocation endpoint (if available) with refresh or access token.
3. Internal: mark refresh token / session jti revoked.
4. Optional OIDC RP-initiated logout.

## 9. Security Controls
| Threat | Control (Client) | Control (Server) |
|--------|------------------|------------------|
| Code interception | PKCE S256; HTTPS App Link | Verify code_verifier strictly |
| Scheme hijack | Prefer App Link | Reject unexpected redirect hosts |
| Token theft (device) | Encrypted storage | Short TTL + refresh rotation |
| Replay refresh token | Rotation; detect reuse | Store jti; revoke old |
| Phishing / WebView | System browser only | Enforce no embedded flows |
| Audience confusion | Validate aud per call | Enforce provider registry |
| Stale JWK keys | Refresh on unknown kid | Cache + TTL + fallback fetch |
| Id token injection | Validate nonce & iss | Strict signature + nonce |

## 10. Data Models (Internal Suggestion)
ProviderConfig(id, issuer, auth_endpoint, token_endpoint, jwks_uri, scopes[])
UserIdentityLink(user_id, provider_id, provider_subject, created_at, last_login_at)
Session(session_id, user_id, provider_id, provider_refresh_token_encrypted?, rotation_counter, last_refresh_at)
Revocation(token_id/jti, revoked_at, reason)

## 11. Error Handling Matrix
| Phase | Error | Action |
|-------|-------|--------|
| Redirect | access_denied | Prompt retry / alternative provider |
| Token exchange | invalid_grant | Restart flow; new PKCE pair |
| Refresh | invalid_grant | Force full re-auth |
| API | 401 invalid_token | Attempt refresh → fallback re-auth |
| API | 403 | Insufficient scope → adjust requested scopes |

## 12. Multi-Provider Normalization
- Internal subject = hash(provider_id + provider_subject)
- Align claims: email_verified, locale, name, picture
- Link accounts on matching verified email (user consent)
- Provider-specific scope translation → internal roles

## 13. Performance & Caching
- JWK cache per issuer (12h TTL; early refresh on unknown kid)
- Introspection caching (30–60s) if opaque tokens
- Downstream provider call minimization: persist essential profile fields locally

## 14. Observability (Minimal Set)
Events: auth_start, auth_code_received, token_exchange_success/failure, jwk_cache_miss, refresh_rotation, token_validation_failure(reason)
Metrics: login_success_total{provider}, token_refresh_total{provider}, validation_fail_total{reason}

## 15. Direct vs Backend-Assisted Decision Table
| Requirement | Direct | Backend-Assisted |
|-------------|--------|------------------|
| Simplicity | ✅ | |
| Central claim enrichment | | ✅ |
| Hide provider tokens from device | | ✅ |
| Custom internal roles | | ✅ |
| Minimal infra | ✅ | |
| Fine-grained revocation | | ✅ |
| Reduce provider API exposure | | ✅ |

## 16. Happy Path (Backend-Assisted)
1. App builds PKCE + state + nonce → open auth URL
2. Provider authenticates & consents
3. Redirect → backend /cb with code & state
4. Backend validates state → exchanges code (incl. code_verifier)
5. Validate id_token (sig, iss, aud, exp, nonce)
6. Map / create internal user; store provider refresh token (encrypted)
7. Mint internal access (10–15m) + refresh (30d) tokens
8. App retrieves internal tokens (poll or one-time code redeem)
9. App uses internal access token → API
10. Backend uses provider token if external data needed
11. App refreshes before expiry
12. Logout → revoke internal + optional provider refresh

## 17. Happy Path (Direct)
1. App builds PKCE auth URL
2. User authenticates at provider
3. Redirect to app with code
4. App exchanges code + code_verifier
5. Validate id_token locally
6. Store tokens securely
7. Call API with provider access_token
8. API validates token signature & claims
9. Refresh when near expiry
10. Logout & revoke if supported

## 18. Validation Checklist (Server)
For each protected request (Direct case):
- Authorization header present & Bearer
- Distinguish internal vs provider token (issuer) 
- If provider JWT: verify sig (kid → JWK), iss, aud, exp, nbf
- If internal JWT: verify sig + jti not revoked
- Map subject → user
- Enforce scope/role policy

## 19. Common Pitfalls
| Pitfall | Effect | Mitigation |
|---------|--------|------------|
| Reusing code_verifier | Replay window | Generate fresh per attempt |
| Mismatched redirect_uri | invalid_grant | Exact match only |
| Skipping nonce | id_token injection risk | Always store & validate |
| Assuming refresh returned | Unexpected 401 later | Handle absence & re-auth |
| Plaintext token storage | Easier device compromise | Encrypted storage APIs |
| No JWK rotation logic | Sudden validation failures | Cache + refetch on miss |
| Logging tokens | Secret leakage | Truncate + hash |

## 20. Enhancement Roadmap
1. Start Direct PKCE
2. Add robust server validation
3. Introduce refresh & rotation handling
4. Add observability + metrics
5. Introduce brokerage (internal JWT)
6. Add revocation & logout endpoints
7. Support multi-provider linking

## 21. Cross References
See: [[Authorization Code Grant]], [[Authorization Endpoint]], [[Token Endpoint]], [[PCKE-Protocol]], [[Terminology]], [[Tokens in OAuth]] for foundational definitions and parameter rules.

## 22. Minimal Condensed Sequence
1. Register & configure
2. Generate PKCE + state + nonce
3. Open auth URL
4. Receive code
5. Exchange code + code_verifier
6. Validate tokens
7. Call API
8. Refresh
9. Revoke / logout

> [!tip] You can convert this file into a diagram later or split subsections into dedicated pages if they grow.
