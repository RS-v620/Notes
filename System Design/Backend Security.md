---
# Backend Security for OAuth 2.0 Resource Servers
#oauth2 #security #backend #hardening
---

> Comprehensive security guidance for backends that accept OAuth 2.0 / OIDC tokens from external providers and issue internal tokens. Covers threat model, hardening layers, monitoring, incident response, and prioritized checklists.

## 0. Security Mindset
Your backend is a high-value target:
- It holds user identity links, provider refresh tokens (if stored), and signing keys.
- Compromise enables impersonation, data exfiltration, lateral movement.
- Defense-in-depth: assume layers fail; build redundancy.

Core principles:
1. Least privilege (IAM, network, process permissions)
2. Defense in layers (network → TLS → auth → authorization → audit)
3. Fail securely (deny by default, explicit allow)
4. Detect & respond fast (monitoring, alerting, runbooks)
5. Zero trust internal boundaries (validate even internal calls)

## 1. Token Security (Validation & Storage)

### 1.1 Validating External Provider Tokens
Always perform ALL checks (never skip for "trusted" sources):

**For JWT (id_token or JWT access_token):**
- [ ] Parse header; extract `alg`, `kid`
- [ ] Reject if `alg` is `none`, `HS256` when expecting `RS256`/`ES256`, or unknown
- [ ] Fetch JWK from cached provider JWKS (see caching below)
- [ ] Verify signature with matching `kid` public key
- [ ] Validate `iss` == expected provider issuer (exact string match)
- [ ] Validate `aud` contains your `client_id` or expected audience
- [ ] Check `exp` > current time (with small grace for clock skew, e.g., 60s)
- [ ] Check `nbf` <= current time (if present)
- [ ] For id_token at login: validate `nonce` matches stored nonce from auth request
- [ ] Optional: validate `azp` (authorized party) if multi-client
- [ ] Reject if any check fails → 401 with minimal detail ("invalid_token")

**For Opaque Tokens:**
- [ ] Call provider introspection endpoint (cache result briefly, 30–60s)
- [ ] Check `active: true`
- [ ] Validate `client_id`, `scope`, `exp`
- [ ] Rate-limit introspection calls (per IP, per token prefix)

### 1.2 JWK Caching & Rotation
- Cache JWK sets per issuer; key by (issuer, kid)
- TTL: 12–24h default
- On unknown `kid`: refetch immediately (max 1 retry per request)
- Monitor key rotation events (log when new `kid` appears)
- Use atomic cache updates to prevent race conditions
- Fallback: if cache & refetch both fail → reject token (fail closed)

### 1.3 Storing Provider Refresh Tokens
If you store provider refresh tokens (broker pattern):
- Encrypt at rest with envelope encryption:
  - Data Encryption Key (DEK) per token or per user
  - Key Encryption Key (KEK) in HSM or KMS (AWS KMS, GCP KMS, Azure Key Vault, HashiCorp Vault)
- Never log refresh tokens (truncate to first 8 chars + hash)
- Rotate KEK periodically (quarterly)
- Revoke stored tokens when user logout or suspicious activity detected
- Use database-level encryption (TDE) as second layer

### 1.4 Internal Token Security
If you mint your own access/refresh tokens:
- Use asymmetric signing (RS256/ES256) so resource servers validate with public key only
- Store private key in KMS; fetch at startup, cache in memory, refresh daily
- Short TTL for access (10–15 min); longer for refresh (7–30 days with rotation)
- Include `jti` (unique ID) in refresh tokens for revocation tracking
- Maintain revocation store (Redis with TTL = refresh token max lifetime)
- Sign with key versioning (`kid`) to support rotation without breaking existing tokens

## 2. Secrets Management

### 2.1 Never Hardcode
- No secrets in code, config files checked into git, environment variable defaults
- Use secret managers: AWS Secrets Manager, GCP Secret Manager, Azure Key Vault, HashiCorp Vault
- Inject at runtime (container startup, lambda env, K8s secrets)

### 2.2 Confidential Client Secrets (if using backend OAuth client)
- Store `client_secret` in secret manager, not env vars
- Rotate quarterly or on suspected compromise
- Use different secrets per environment (dev/staging/prod)
- Audit access logs for secret retrieval

### 2.3 Database Credentials
- Use IAM authentication if supported (AWS RDS IAM, GCP Cloud SQL IAM)
- Rotate passwords automatically (30–90 days)
- Least privilege: app user != admin; read-only replicas for analytics
- Network isolation: DB not publicly accessible

### 2.4 API Keys & Service Tokens
- Scope to minimum required permissions
- Set expiration where possible
- Rotate on schedule or post-incident
- Log all API key usage for anomaly detection

## 3. Network & Infrastructure Security

### 3.1 TLS Everywhere
- Enforce TLS 1.2+ (prefer 1.3)
- Use strong cipher suites (disable CBC, RC4, 3DES)
- Certificate pinning (optional, for critical provider connections)
- HSTS headers (Strict-Transport-Security: max-age=31536000; includeSubDomains)
- No mixed content (all assets over HTTPS)

### 3.2 Network Segmentation
- Backend in private subnet; only load balancer publicly accessible
- Database in isolated subnet; security group allows only backend IPs
- Zero external DB access; use bastion host or VPN for admin
- Egress filtering: allow only required external endpoints (provider token/JWK URLs)

### 3.3 Firewall & Security Groups
- Default deny; explicit allow rules
- Rate limiting at load balancer / WAF (e.g., AWS WAF, Cloudflare)
- DDoS protection enabled
- Block known malicious IPs (integrate threat feeds)

### 3.4 Container / VM Hardening
- Run as non-root user
- Read-only filesystem where possible
- No SSH access to production containers (use ephemeral access logs)
- Minimal base images (distroless, Alpine)
- Scan images for CVEs (Trivy, Snyk, Clair)
- Auto-patching for OS & dependencies

## 4. Authentication & Authorization

### 4.1 API Gateway / Middleware
- Centralized token validation middleware
- Reject requests without Authorization header or invalid Bearer format
- Extract & validate token before routing to business logic
- Attach user context (subject, roles) to request for downstream use

### 4.2 Internal Service-to-Service Auth
- Use mutual TLS (mTLS) or service mesh (Istio, Linkerd)
- Or: issue internal service tokens (short-lived, scoped to service)
- Never trust internal calls blindly (defense against lateral movement)

### 4.3 Role-Based Access Control (RBAC)
- Map provider scopes → internal roles at provisioning time
- Enforce roles at API endpoints (middleware or decorators)
- Principle of least privilege: default deny, explicit grants
- Audit role assignments; periodic review (quarterly)

### 4.4 Rate Limiting & Throttling
Per endpoint + per user/IP:
- Token validation: 100 req/min per IP
- Refresh token: 10 req/min per user
- Introspection: 30 req/min per token
- JWK fetch: 5 req/min per issuer
Use sliding windows or token bucket algorithms

## 5. Logging, Monitoring & Alerting

### 5.1 Security Event Logging
Log (with PII minimization):
- All authentication attempts (success/failure, provider, truncated token)
- Token validation failures (reason: exp, iss, aud, sig, unknown_kid)
- JWK cache misses & refetches
- Refresh token rotations & reuse detection
- Introspection calls (token prefix, result)
- Failed authorization (403s with user + endpoint)
- Secret retrieval (from KMS/Vault)
- Admin actions (user provisioning, role changes)

Never log:
- Full tokens (truncate to 8 chars + hash remainder)
- Passwords, PINs, secrets
- Full PII unless required for fraud investigation (use hashed IDs)

### 5.2 Structured Logs
Use JSON format with standard fields:
- timestamp (ISO8601, UTC)
- severity (DEBUG, INFO, WARN, ERROR, CRITICAL)
- event_type (auth_success, token_validation_fail, jwk_refresh)
- user_id (internal, hashed if sensitive)
- request_id (correlation)
- ip_address (for rate limiting & geo analysis)
- user_agent (detect automation)

### 5.3 Metrics & Dashboards
Track:
- Authentication success/failure rates (by provider)
- Token validation failure breakdown (exp, sig, aud, iss)
- Refresh token rotation count & reuse alerts
- JWK cache hit rate & unknown kid events
- API latency (p50, p95, p99)
- Error rates (4xx, 5xx by endpoint)
- Rate limit triggers

### 5.4 Alerts (High Priority)
Trigger pages/escalations on:
- Sudden spike in token validation failures (>5% over baseline)
- Unknown `kid` for established provider (possible key rotation or attack)
- Refresh token reuse detected (potential theft)
- Multiple failed auth from single IP (>10/min)
- Secrets access from unexpected service/IP
- Database connection failures
- Certificate expiration (< 7 days warning)

## 6. Incident Response

### 6.1 Detection Signals
Compromised backend often shows:
- Unusual token validation patterns (mass 401s or unexpected successes)
- Introspection spikes or novel token prefixes
- JWK fetch failures or cache poisoning attempts
- New users provisioned from unexpected IPs/geos
- Sudden role escalation or admin account creation
- Secrets accessed outside maintenance windows

### 6.2 Immediate Response (CRITICAL)
If breach suspected:
1. **Isolate**: Block traffic to compromised service (security group / WAF rule)
2. **Revoke**: Invalidate all internal refresh tokens (flush revocation store or increment key version)
3. **Rotate**: Rotate signing keys, database passwords, API keys, KMS keys
4. **Audit**: Pull logs for last 30 days; identify scope (which users, data accessed)
5. **Notify**: Inform security team, legal, affected users (if PII exposed)
6. **Forensics**: Preserve snapshots (disk, memory, logs) before cleanup

### 6.3 Post-Incident
- Root cause analysis (RCA)
- Update threat model
- Add detection rules for novel attack vectors
- Improve hardening (e.g., add mTLS, stricter RBAC)
- Conduct tabletop exercises quarterly

## 7. Compliance & Auditing

### 7.1 Regulatory Requirements
Depending on jurisdiction & data:
- GDPR: right to erasure, data portability, breach notification (72h)
- CCPA: disclosure, opt-out, deletion
- HIPAA: PHI encryption, access logs, BAAs
- PCI-DSS: cardholder data encryption, network segmentation
- SOC 2: security controls, annual audits

### 7.2 Audit Trails
Immutable logs for:
- User identity linking & delinking
- Role/permission changes
- Token issuance & revocation
- Admin access to secrets or production DBs
- Data exports or bulk operations

### 7.3 Penetration Testing
- Annual external pentest (OWASP Top 10, OAuth-specific)
- Quarterly internal security reviews
- Bug bounty program (if mature)

## 8. Prioritized Security Checklist

### CRITICAL (Implement First)
- [ ] Validate ALL JWT claims (iss, aud, exp, sig) on every request
- [ ] Encrypt provider refresh tokens at rest (KMS envelope encryption)
- [ ] Store signing keys in KMS/HSM, never in code or env vars
- [ ] Enforce TLS 1.2+ everywhere; HSTS enabled
- [ ] Backend in private subnet; DB not publicly accessible
- [ ] Rate limit token endpoints (10 req/min per user for refresh)
- [ ] Log all auth events; alert on validation failure spikes
- [ ] Maintain revocation store for internal refresh tokens
- [ ] Rotate secrets quarterly (client_secret, DB passwords, signing keys)

### HIGH (Within 30 Days)
- [ ] JWK caching with TTL & unknown kid refetch
- [ ] Introspection result caching (30–60s TTL)
- [ ] mTLS or service tokens for internal service calls
- [ ] RBAC enforcement at API middleware layer
- [ ] Structured logging with request_id correlation
- [ ] Metrics dashboard (auth success/fail, latency, errors)
- [ ] Incident response runbook documented
- [ ] Secret rotation automation (scripts or IaC)
- [ ] Container image CVE scanning in CI/CD
- [ ] Database connection via IAM authentication

### MEDIUM (Within 90 Days)
- [ ] Certificate pinning for critical provider endpoints
- [ ] Automated key rotation for internal signing keys
- [ ] Token introspection rate limiting per IP
- [ ] Geo-blocking or anomaly detection (unusual login locations)
- [ ] Read-only DB replicas for analytics
- [ ] DDoS protection & WAF rules tuned
- [ ] Annual pentest scheduled
- [ ] Compliance audit prep (SOC 2, ISO 27001)
- [ ] Backup & disaster recovery tested

### LOW (Ongoing / Nice-to-Have)
- [ ] Bug bounty program
- [ ] Automated compliance scanning (IaC policies)
- [ ] Advanced anomaly detection (ML-based)
- [ ] Zero-trust segmentation (micro-perimeters)
- [ ] Hardware security modules (HSM) for highest-value keys

## 9. Threat Scenarios & Mitigations

| Threat | Attack Vector | Impact | Mitigation |
|--------|---------------|--------|------------|
| Token theft (device) | Stolen phone, malware | Impersonation | Short TTL (10m), refresh rotation, device binding (advanced) |
| Token theft (network) | MitM, DNS hijack | Impersonation | TLS 1.3, certificate pinning, HSTS |
| Replay attack | Intercepted refresh token | Unauthorized access | Rotation + reuse detection (revoke on reuse) |
| JWK cache poisoning | Compromised upstream | Accept forged tokens | Pin expected kid set, alert on new kids, TLS validation |
| SQL injection | Unsanitized input | DB compromise, data exfil | Parameterized queries, ORM, least privilege DB user |
| Secrets leakage (logs) | Full token in logs | Mass impersonation | Truncate tokens in logs (8 chars + hash), secret scrubbing |
| Secrets leakage (repo) | Hardcoded in code | Instant compromise | Pre-commit hooks (git-secrets, trufflehog), secret scanning in CI |
| Lateral movement | Compromised service | Access to other services | mTLS, service mesh, zero-trust internal |
| Privilege escalation | Role manipulation | Admin takeover | RBAC enforcement, audit role changes, immutable logs |
| DDoS | Flood token endpoints | Service outage | Rate limiting, WAF, auto-scaling, CDN |
| Insider threat | Malicious employee | Data theft, sabotage | Least privilege IAM, audit trails, separation of duties |

## 10. Secure Development Practices

### 10.1 Code Review & Static Analysis
- Peer review all auth/token logic
- Run SAST tools (SonarQube, Semgrep, Snyk Code)
- Check for OWASP Top 10 (injection, broken auth, XSS, etc.)
- Dependency scanning (npm audit, pip-audit, Dependabot)

### 10.2 Secrets in CI/CD
- Use secret injection (GitHub Secrets, GitLab CI vars, Vault)
- Never print secrets in build logs
- Rotate CI/CD tokens quarterly
- Limit CI service account permissions

### 10.3 Immutable Infrastructure
- Treat servers as cattle, not pets
- Deploy via IaC (Terraform, CloudFormation, Pulumi)
- Blue-green or canary deployments
- Rollback strategy tested

### 10.4 Least Privilege IAM
- One IAM role per service (not shared admin keys)
- Time-bound credentials (AWS STS, GCP Workload Identity)
- MFA for human access
- No long-lived access keys

## 11. Example: Secure Token Validation Middleware (Pseudocode)

```pseudo
function validateBearerToken(request):
    authHeader = request.headers["Authorization"]
    if not authHeader or not authHeader.startsWith("Bearer "):
        return 401("missing_token")

    token = authHeader.substring(7)
    tokenType = detectTokenType(token)  // internal vs provider, JWT vs opaque

    if tokenType == "internal_jwt":
        return validateInternalJWT(token)
    else if tokenType == "provider_jwt":
        return validateProviderJWT(token)
    else if tokenType == "opaque":
        return validateOpaqueToken(token)
    else:
        return 401("invalid_token")

function validateProviderJWT(token):
    try:
        header = decodeJWTHeader(token)
        if header.alg not in ["RS256", "ES256", "RS384", "ES384"]:
            log("invalid_alg", alg=header.alg)
            return 401("invalid_token")

        jwk = jwkCache.get(header.iss, header.kid)
        if not jwk:
            jwk = fetchJWK(header.iss, header.kid)
            if not jwk:
                alert("unknown_kid", iss=header.iss, kid=header.kid)
                return 401("invalid_token")
            jwkCache.set(header.iss, header.kid, jwk, ttl=12h)

        if not verifySignature(token, jwk):
            log("signature_fail")
            return 401("invalid_token")

        claims = decodeJWTPayload(token)
        provider = providerRegistry.get(claims.iss)
        if not provider:
            log("unknown_issuer", iss=claims.iss)
            return 401("invalid_token")

        if claims.aud != provider.expectedAudience:
            log("aud_mismatch", expected=provider.expectedAudience, got=claims.aud)
            return 401("invalid_token")

        now = currentUnixTime()
        if claims.exp <= now:
            log("token_expired", exp=claims.exp)
            return 401("invalid_token")

        if claims.nbf and claims.nbf > now + CLOCK_SKEW_GRACE:
            log("not_yet_valid", nbf=claims.nbf)
            return 401("invalid_token")

        user = ensureUserProvisioned(claims.iss, claims.sub, claims)
        attachUserContext(request, user)
        return OK

    catch Exception as e:
        log("validation_exception", error=e)
        return 401("invalid_token")

function ensureUserProvisioned(issuer, subject, claims):
    link = db.findIdentityLink(issuer, subject)
    if link:
        return db.getUser(link.userId)
    else:
        user = db.createUser(email=claims.email, name=claims.name)
        db.createIdentityLink(user.id, issuer, subject)
        log("user_provisioned", userId=user.id, issuer=issuer)
        return user
```

## 12. Example: Secure Secrets Retrieval (KMS Pattern)

```pseudo
// At app startup
function initializeSigningKey():
    keyVersionId = config.get("SIGNING_KEY_VERSION_ID")  // e.g., projects/X/locations/Y/keyRings/Z/cryptoKeys/A/cryptoKeyVersions/1
    kmsClient = createKMSClient(credentials=serviceAccountIAM)

    encryptedKeyMaterial = secretManager.getSecret("signing-key-encrypted")
    plaintextKeyPEM = kmsClient.decrypt(keyVersionId, encryptedKeyMaterial)
    
    signingKeyCache.set("current", plaintextKeyPEM, ttl=24h)
    log("signing_key_loaded", keyVersion=keyVersionId)

// When minting internal tokens
function mintInternalJWT(userId, roles):
    privateKey = signingKeyCache.get("current")
    if not privateKey:
        initializeSigningKey()
        privateKey = signingKeyCache.get("current")

    header = { alg: "RS256", kid: config.get("SIGNING_KEY_KID"), typ: "JWT" }
    payload = {
        sub: userId,
        iss: "https://yourdomain.com",
        aud: "your-api",
        iat: now(),
        exp: now() + 600,  // 10 min
        jti: generateUUID(),
        roles: roles
    }
    return signJWT(header, payload, privateKey)
```

## 13. Monitoring Query Examples (SIEM / Log Aggregator)

Splunk / ELK / CloudWatch Insights:

Detect token validation failure spike:
```
event_type="token_validation_fail" | timechart span=1m count by reason | where count > 50
```

Detect refresh token reuse:
```
event_type="refresh_token_reuse" | stats count by user_id | where count > 1
```

Detect unknown kid in last hour:
```
event_type="unknown_kid" earliest=-1h | table _time, iss, kid
```

Failed auth from single IP:
```
event_type="auth_fail" | stats count by ip_address | where count > 10
```

## 14. Cross-References
See: [[System Design/Flow]], [[PCKE-Protocol]], [[Token Endpoint]], [[Best Practices]], [[Attacks and Mitigations]] for OAuth-specific threats and protocol-level controls.

## 15. Next Steps
- [ ] Review checklist; prioritize CRITICAL items
- [ ] Provision KMS keys (or HSM) for signing & encryption
- [ ] Implement token validation middleware with all checks
- [ ] Set up structured logging & dashboards
- [ ] Document incident response runbook
- [ ] Schedule quarterly secret rotation & annual pentest

> [!tip] Security is iterative. Start with CRITICAL checklist, add layers over time, and continuously test & improve.
