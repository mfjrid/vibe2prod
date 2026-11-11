# Laravel Adapter

Use this adapter only after confirming the project is Laravel and identifying
its exact major/minor version. Do not assume APIs or package compatibility across
versions.

## Discovery targets
- `composer.json`, `composer.lock`, `artisan`, `bootstrap/app.php` (version-dependent),
  `config/`, `routes/`, `app/`, `database/migrations/`, `resources/`, `tests/`.
- Authentication starter kit/custom auth, guards/providers, policies, gates,
  middleware, Form Requests, exception handling, queue and scheduler setup.
- Deployment artifacts: Dockerfiles, Compose, Nginx/Apache, Supervisor/systemd,
  CI pipeline, `.env.example` (never print actual `.env`).

## Security
- Inspect route middleware and route groups, not just middleware class existence.
- Verify throttling is attached to relevant routes; inspect custom RateLimiter keys.
- Check CSRF behavior for web routes and API auth model for API routes.
- Inspect policies/gates and object-level checks in controllers/services.
- Check mass assignment (`$fillable`/`$guarded`), validation, query construction,
  file storage disks, signed URLs, and upload validation.
- Inspect trusted proxy configuration and how client IP is resolved.
- Verify production debug/error configuration without exposing secret values.
- Check session cookie settings, password hashing, reset token behavior, and MFA.

## Operational features
- Scheduler: inspect `routes/console.php` or version-appropriate schedule setup,
  deployment cron/`schedule:run`, and heartbeat monitoring.
- Queue: inspect connection, worker deployment, retries, failed jobs, timeout,
  Horizon if installed, and whether jobs are idempotent.
- Logs: inspect logging channels, rotation, retention, and protected viewer.
- Cache: inspect cache driver and shared-cache requirements for multi-instance.
- Backup: inspect configured database/files, remote destination, retention,
  encryption, notifications, and restore docs.
- Email: inspect mailer configuration presence, queue, logs, and test-send access.
- Migrations: inspect migration history and production deployment procedure.

## Candidate packages (evaluate, do not install automatically)
- Spatie Laravel Permission — roles/permissions.
- Spatie Laravel Activitylog — activity/audit history.
- Spatie Laravel Backup — backup orchestration.
- Laravel Horizon — Redis queue monitoring.
- Laravel Telescope — development diagnostics; restrict/disable appropriately.
- Sentry — exception/performance monitoring.
- A maintained Laravel log viewer — only if safe authorization and path handling
  are confirmed.
- Cloudflare Turnstile — CAPTCHA alternative for selected public forms.

Before recommending any package, verify current compatibility with the installed
Laravel/PHP versions, maintenance status, license, security advisories, and
whether native Laravel features already solve the need.

## Safe commands
Commands such as `php artisan route:list`, `php artisan about`, and
`php artisan migrate:status` may be useful, but first inspect project scripts,
environment, and command side effects. Never run migrations, seeders, cache
clears, queue restarts, or destructive Artisan commands during audit.
