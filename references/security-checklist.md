# Security & Protection Checklist

Audit the actual threat surface. Mark controls N/A only with a reason.

## Authentication and abuse prevention
- [ ] Login rate limiting: per account and/or IP, sensible thresholds, backoff.
- [ ] Registration, password reset, OTP, invitation, and verification throttles.
- [ ] Brute-force detection and alerting.
- [ ] CAPTCHA or proof-of-work only where risk/abuse warrants it; accessible fallback.
- [ ] Honeypot or behavioral bot detection for relevant public forms.
- [ ] Enumeration resistance for login, reset, and account lookup.
- [ ] MFA/2FA for administrators and high-risk accounts where appropriate.
- [ ] Secure password hashing using framework-supported modern algorithms.
- [ ] Password reset tokens are random, short-lived, single-use, and safely stored.
- [ ] Session rotation after login/privilege changes; secure cookie flags.
- [ ] Logout invalidates server-side session/token where applicable.

## Rate limiting and IP controls
- [ ] Rate limits cover sensitive routes, API endpoints, expensive searches, and uploads.
- [ ] Limits are keyed appropriately (user, token, IP, route); proxy trust is correct.
- [ ] Trusted proxy configuration cannot be spoofed by arbitrary clients.
- [ ] Manual IP/CIDR denylist has reason, actor, timestamp, expiry, and audit history.
- [ ] Automatic blocking uses multiple signals or graduated response.
- [ ] Temporary block expiry, allowlist, admin bypass, and emergency recovery exist.
- [ ] IPv4, IPv6, CIDR, shared/NAT IP, and reverse proxy behavior considered.
- [ ] Admin can review, appeal/unblock, and see why a block occurred.
- [ ] No unbounded attacker-controlled block-list growth or easy self-DoS.

## Input, output, and request security
- [ ] Server-side validation for all untrusted input.
- [ ] Context-aware output escaping; safe rich-text sanitization.
- [ ] Parameterized queries/ORM; no unsafe SQL concatenation.
- [ ] CSRF protection for cookie-authenticated state-changing requests.
- [ ] CORS is restrictive and intentional.
- [ ] SSRF protections for URL fetchers, webhooks, importers, and image proxies.
- [ ] Open redirect protections.
- [ ] Path traversal protections.
- [ ] Mass-assignment protections and explicit writable fields.
- [ ] Request size, upload size, and execution limits.
- [ ] Content Security Policy appropriate to the app; avoid unsafe broad policies.
- [ ] HSTS enabled only when HTTPS is correctly deployed.
- [ ] X-Content-Type-Options, frame protection, Referrer-Policy, and Permissions-Policy.
- [ ] Error pages do not expose stack traces, SQL, paths, or secrets in production.

## File and media handling
- [ ] Allowlist extensions and verify actual MIME/content where possible.
- [ ] Randomized storage names; prevent executable uploads.
- [ ] Private files stored outside public web root or served through authorization.
- [ ] Image/document processing is resource-limited and safely isolated.
- [ ] Malware scanning considered for high-risk user uploads.
- [ ] Download endpoints enforce authorization and safe content disposition.

## Authorization and application logic
- [ ] Server-side authorization on every protected action/resource.
- [ ] Object-level authorization prevents IDOR/BOLA.
- [ ] Role/permission checks are deny-by-default.
- [ ] Tenant/organization boundaries enforced in queries and jobs.
- [ ] Sensitive admin actions require appropriate privilege and audit trail.
- [ ] API tokens have scopes, expiry/revocation, and least privilege.
- [ ] Webhooks verify signatures, timestamps, and replay/deduplication protection.
- [ ] Financial and state-mutating endpoints enforce idempotency keys to prevent duplicate transaction replay.
- [ ] Business logic is hardened against race condition exploitation (e.g., parallel coupon redemption, balance overdraft, concurrent voucher claims).

## Dependencies, secrets, and deployment
- [ ] Dependencies and runtime versions are supported and patched.
- [ ] Lockfiles are committed and dependency audit is available.
- [ ] Secrets are outside source control and are not exposed to frontend.
- [ ] Debug mode disabled in production.
- [ ] Production error reporting is configured without leaking sensitive payloads.
- [ ] Database and service accounts use least privilege.
- [ ] HTTPS enforced; TLS and certificate renewal ownership understood.
- [ ] Admin surfaces are protected; default credentials removed.
- [ ] Security configuration differs safely by environment.
- [ ] Security-sensitive changes have tests.

## Required evidence
Record route/middleware/controller/policy/config/test references. For runtime
claims, state how they were verified. Do not run intrusive tests against
production without explicit authorization.
