# Maintenance & System Diagnostics Checklist

## Maintenance operations
- [ ] Maintenance mode is available and safe for deployment windows.
- [ ] Cache clear/rebuild commands are documented and authorized.
- [ ] Route/config/view cache operations match framework and environment.
- [ ] Queue restart and scheduler operations are documented.
- [ ] Migration status is visible; migrations are reviewed before production.
- [ ] Application version/build/commit is identifiable.
- [ ] Changelog/release notes are maintained where appropriate.
- [ ] Feature flags or safe rollout controls exist where useful.

## System information
- [ ] Runtime/framework/database versions are visible to authorized admins.
- [ ] Required extensions/services are checked.
- [ ] Disk/storage, queue, scheduler, and database connectivity are checked.
- [ ] Diagnostics reveal status, not secrets, environment values, or credentials.
- [ ] System info is not public and is permission-gated.
- [ ] Health endpoints reveal only minimal information to unauthenticated callers.

## Admin tools
- [ ] Route list viewer is restricted and does not reveal sensitive routes publicly.
- [ ] Database/table browser is read-only by default and strongly restricted.
- [ ] Configuration checker reports missing/unsafe keys without printing values.
- [ ] Dependency information is sanitized and not an unnecessary attack surface.
- [ ] Dangerous maintenance actions require confirmation and audit logging.
- [ ] Operations are compatible with containers, multiple instances, and deployment model.

System information pages should not expose phpinfo(), `.env`, raw config dumps,
filesystem paths, internal IPs, or detailed infrastructure topology to ordinary
users.
