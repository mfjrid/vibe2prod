# Node.js Adapter

Confirm runtime, framework, package manager, deployment model, and whether the
project is server-rendered, API-only, or frontend-only.

Inspect `package.json`, lockfile, server/bootstrap files, routers, middleware,
auth/session setup, ORM/database layer, workers/queues, CI, container and proxy
configuration.

Audit:
- Request validation and schema validation at trust boundaries.
- Authentication, authorization, object-level access, and tenant isolation.
- Rate limiting and correct proxy/IP handling.
- CSRF for cookie-authenticated browser flows.
- CORS allowlists and security headers.
- Secure cookies, session/token expiry and revocation.
- Output encoding, template escaping, and safe DOM handling.
- SQL/NoSQL injection and safe query construction.
- SSRF, open redirects, path traversal, and upload safety.
- Dependency audit, lockfile, supported Node.js version.
- Structured logs, redaction, error monitoring, and request IDs.
- Queue retries, idempotency, scheduler and worker health.
- Backup/restore process and operational runbooks.
- Production secrets, debug behavior, health checks, and deployment rollback.

Do not recommend middleware or packages without checking compatibility and
whether existing framework features already provide the control.
