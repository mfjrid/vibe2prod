# Generic Web Adapter

Use when the stack is unknown, unsupported, or distributed across multiple
services.

Map the application into:
- browser/client;
- edge/CDN/WAF/reverse proxy;
- web/API server;
- authentication/identity provider;
- database and cache;
- file/object storage;
- background workers and scheduler;
- external services;
- deployment/monitoring/backup infrastructure.

Audit controls at the layer where they are actually enforced. For example,
rate limiting may live at CDN, gateway, application, or multiple layers.
Do not mark it missing in the application if a verified edge control provides
the required coverage; document the boundary and dependency.

For every finding, state which team/vendor/hosting layer owns remediation.
If the agent cannot inspect infrastructure configuration, classify as
UNVERIFIED rather than MISSING.
