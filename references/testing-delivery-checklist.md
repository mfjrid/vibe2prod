# Testing, CI/CD & Delivery Checklist

- [ ] Unit tests cover important business logic.
- [ ] Feature/integration tests cover authentication, authorization, and critical workflows.
- [ ] Regression tests exist for fixed defects.
- [ ] Validation and error paths are tested.
- [ ] Security-sensitive controls have tests (rate limits, permissions, uploads, etc.).
- [ ] CI runs tests, lint/format, static analysis, and build checks as appropriate.
- [ ] Dependency/security checks are available.
- [ ] Secrets are not embedded in CI logs or artifacts.
- [ ] Deployment process is documented and repeatable.
- [ ] Migrations and rollback strategy are reviewed.
- [ ] Environment variables and required services are documented.
- [ ] Staging/pre-production verification exists for risky releases.
- [ ] Smoke tests and post-deploy health checks exist.
- [ ] Release/rollback ownership is clear.
- [ ] Client handover includes admin guide, backup/recovery, and support boundaries.

Do not claim a deployment is safe solely because the application builds.
