# Native PHP Adapter

Confirm PHP version, web server, entry point, routing approach, and dependency
manager. Inspect `composer.json`/lockfile if present.

Audit:
- PDO or another parameterized database access layer.
- Password hashing with `password_hash` / `password_verify` or supported library.
- Session cookie flags, session ID regeneration, and session invalidation.
- CSRF tokens on state-changing forms.
- Context-aware output escaping and safe HTML sanitization.
- Centralized validation and authorization checks.
- Secure upload handling, randomized names, and non-public storage.
- Rate limiting backed by shared storage when multi-instance.
- Error handling that hides details in production.
- Secrets outside web root and source control.
- Dependency updates and lockfile.
- Logging with redaction and rotation.
- Backup and restore process independent of application UI.

Do not assume custom helper functions are safe; inspect call sites and tests.
Recommend adopting a maintained framework or well-supported components only when
it materially reduces risk and fits project constraints.
