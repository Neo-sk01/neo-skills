# Security Checklist Reference

Lookup checklist for security review. Referenced by skills — not a skill itself.

---

## Input Validation

- [ ] All user input validated on the server side (client validation is UX, not security)
- [ ] Input length limits enforced
- [ ] Input type/format validated (email, URL, date — use proven validators)
- [ ] File uploads: type whitelist, size limit, name sanitization, stored outside webroot
- [ ] Path traversal: reject `..`, normalize paths before use
- [ ] JSON/XML: parse with safe defaults (no external entities, no prototype pollution)

## Authentication

- [ ] Passwords hashed with bcrypt/scrypt/argon2 (never MD5/SHA)
- [ ] Rate limiting on login/registration endpoints
- [ ] Account lockout or progressive delays after failed attempts
- [ ] Session tokens: cryptographically random, sufficient length (>=128 bits)
- [ ] Tokens expire and can be revoked
- [ ] Password reset: time-limited, single-use tokens
- [ ] Multi-factor authentication available for sensitive operations

## Authorization

- [ ] Every endpoint checks both authentication AND authorization
- [ ] Default deny — require explicit grants, not explicit denials
- [ ] No client-side-only authorization checks
- [ ] Resource access validated: user can only access their own data
- [ ] Admin endpoints behind separate auth with audit logging
- [ ] API keys scoped to minimum required permissions

## Data Protection

- [ ] No secrets in source code, logs, or error messages
- [ ] PII encrypted at rest
- [ ] TLS for all data in transit
- [ ] Database queries parameterized (no string concatenation)
- [ ] API responses return only required fields (no over-fetching)
- [ ] Sensitive data masked in logs (credit cards, SSNs, tokens)

## HTTP Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

## CORS

- [ ] Allowed origins explicitly listed (never `*` with credentials)
- [ ] Only required methods allowed
- [ ] Only required headers exposed
- [ ] Preflight cache configured (`Access-Control-Max-Age`)

## OWASP Top 10 Quick Reference

1. **Broken Access Control** — enforce server-side, default deny
2. **Cryptographic Failures** — TLS everywhere, strong hashing, no custom crypto
3. **Injection** — parameterized queries, escaped output, validated input
4. **Insecure Design** — threat model, abuse cases, defense in depth
5. **Security Misconfiguration** — minimal installs, no defaults, no verbose errors
6. **Vulnerable Components** — audit dependencies, patch quickly
7. **Auth Failures** — strong passwords, MFA, rate limits
8. **Data Integrity** — verify sources, sign updates, validate CI/CD
9. **Logging Failures** — log security events, alert on anomalies, no PII in logs
10. **SSRF** — validate URLs, whitelist destinations, block internal networks
