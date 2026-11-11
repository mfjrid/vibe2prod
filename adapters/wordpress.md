# WordPress Adapter

Confirm WordPress version, PHP version, hosting model, multisite status, and
whether a custom theme/plugin or page builder controls key behavior.

## Discovery
Inspect `wp-config.php` without printing secrets, active theme, plugins,
mu-plugins, roles/capabilities, REST routes, cron, uploads, rewrite rules,
web server/CDN/WAF configuration, and backup provider.

## Audit focus
- Updates for WordPress core, themes, and plugins; remove abandoned/unused code.
- Unique admin accounts, least privilege, MFA, login protection, and recovery.
- XML-RPC and REST API exposure are intentional, not blindly disabled.
- Application passwords and API integrations are scoped and revocable.
- File editing in dashboard is disabled where operationally appropriate.
- Upload restrictions and executable-file prevention in uploads.
- Nonces and capability checks in custom admin actions/AJAX/REST endpoints.
- Escaping, sanitization, prepared SQL, and secure custom plugin development.
- Login/register/comment/contact form abuse protection.
- Backups include database and uploads; off-site copies and restore tests exist.
- Security plugin presence is not treated as proof of secure configuration.
- SEO plugin settings, canonical, sitemap, robots, schema, OG metadata, redirects.
- Cache/CDN rules do not cache authenticated/admin/personalized responses.
- Debug log is protected and disabled/rotated in production.
- Cron behavior is understood, especially when relying on WP-Cron.
- Admin diagnostics and plugin file viewers are strongly restricted.

Do not disable XML-RPC, REST API, or plugins without understanding integrations
and user impact. Do not edit production `wp-config.php` or database during audit.
