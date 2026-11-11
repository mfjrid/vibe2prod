# Logging, Activity & Observability Checklist

## Application logs
- [ ] Structured logs with timestamp, level, environment, correlation/request ID.
- [ ] Exceptions are captured with useful context and stack trace in protected storage.
- [ ] Secrets, passwords, tokens, payment data, and unnecessary personal data are redacted.
- [ ] Log levels are appropriate; debug verbosity is disabled in production.
- [ ] Rotation/retention prevents unbounded disk growth.
- [ ] Disk-full and logging failures are considered.
- [ ] Centralized log aggregation is considered for multi-server deployments.

## Admin log viewer
- [ ] Strong admin authorization and least privilege.
- [ ] Path traversal and arbitrary file-read protections.
- [ ] Sensitive values are redacted.
- [ ] Pagination, filtering, and bounded file reads.
- [ ] Download/export is protected and audited.
- [ ] Viewer does not expose logs to ordinary users or public routes.

## Activity and audit trail
- [ ] Important create/update/delete, approval, export, permission, and admin actions logged.
- [ ] Actor, action, subject, timestamp, outcome, and relevant before/after changes recorded.
- [ ] Sensitive fields are excluded or masked.
- [ ] Audit records are append-only or tamper-evident where risk requires.
- [ ] Activity history is access-controlled and retained appropriately.
- [ ] Background jobs and system actors are distinguishable from human users.

## Operational monitoring
- [ ] Health/readiness endpoints expose minimal non-sensitive status.
- [ ] Error-rate and latency monitoring exists where needed.
- [ ] Queue depth, failed jobs, scheduler heartbeat, and worker status monitored.
- [ ] CPU, memory, disk, database, and storage monitoring handled at the right layer.
- [ ] Alerts have owners, thresholds, and actionable runbooks.
- [ ] External uptime monitoring is considered for critical services.
- [ ] Logs/metrics/traces use consistent correlation identifiers where useful.

A dashboard displaying server details is not a substitute for monitoring or
alerting. Distinguish application-level diagnostics from host-level telemetry.
