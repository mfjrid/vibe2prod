# User, Role & Access Management Checklist

- [ ] User lifecycle: invite/register, verify, activate, suspend, deactivate, delete.
- [ ] Role and permission model is explicit and documented.
- [ ] Authorization is enforced server-side, not only by hiding UI controls.
- [ ] Least privilege and deny-by-default are applied.
- [ ] Admin accounts receive stronger protection (MFA, rate limits, audit).
- [ ] Password reset and account recovery are secure.
- [ ] Sessions/tokens can be listed and revoked where appropriate.
- [ ] Force logout/revoke-all works for compromised accounts.
- [ ] User profile fields are validated and privacy-aware.
- [ ] Login history and failed attempts are available to authorized roles.
- [ ] Role/permission changes are audited.
- [ ] Tenant/department/project data boundaries are enforced.
- [ ] Inactive accounts and orphaned accounts are reviewed.
- [ ] Service accounts and API credentials have owners and rotation paths.
- [ ] Sensitive exports and bulk actions have additional authorization/audit.

Do not assume a role-management screen proves authorization is enforced in every
route, API, background job, and object-level operation.
