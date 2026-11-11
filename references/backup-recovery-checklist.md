# Backup, Restore & Disaster Recovery Checklist

## Scope and policy
- [ ] Identify critical databases, uploaded files, documents, object storage,
      configuration, encryption keys, and other stateful services.
- [ ] Define RPO (acceptable data loss) and RTO (acceptable recovery time).
- [ ] Define backup frequency, retention, ownership, and storage cost.
- [ ] Backups are encrypted in transit and at rest where appropriate.
- [ ] Backup storage is isolated from application credentials and compromise.
- [ ] At least one off-site or separate-account copy exists for important systems.
- [ ] Retention protects against accidental deletion/ransomware.

## Execution and integrity
- [ ] Database backups are transactionally consistent or use an appropriate method.
- [ ] File backups include required uploads and exclude caches/build artifacts safely.
- [ ] Backup jobs report success/failure and alert an owner.
- [ ] Backup history includes timestamp, scope, size, checksum, and status.
- [ ] Retention cleanup cannot delete the only valid recovery point.
- [ ] Backups are monitored for abnormal size, missing runs, and corruption.
- [ ] Credentials and encryption keys needed for restore are recoverable securely.
- [ ] Restore procedure documents ordering, dependencies, and permissions.

## Restore readiness
- [ ] Restore is tested periodically in an isolated environment.
- [ ] Database and file consistency is verified after restore.
- [ ] Application boots and key workflows work after restore.
- [ ] Recovery time is measured against RTO.
- [ ] Restore is protected against accidental execution on production.
- [ ] Rollback and post-restore validation are documented.

## UI and administration
- [ ] Only authorized administrators can trigger/download/delete backups.
- [ ] Backup artifacts are not publicly accessible.
- [ ] Restore requires explicit confirmation and preferably a second safeguard.
- [ ] Actions are logged with actor, timestamp, target, and outcome.
- [ ] Large jobs run asynchronously and expose progress/status.
- [ ] Download links expire and are access-controlled.

Never run a restore, delete a backup, or trigger a production backup during audit
without explicit approval. A successful backup command alone does not prove
recoverability.
