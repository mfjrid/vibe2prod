# Email & Notification Checklist

- [ ] SMTP/provider settings are environment-specific and secrets are protected.
- [ ] Test-email function is restricted to authorized administrators.
- [ ] Test sends require a deliberate recipient and clear confirmation.
- [ ] Sender domain has appropriate SPF, DKIM, and DMARC configuration where applicable.
- [ ] Queue-based delivery is used for slow/non-critical user requests where appropriate.
- [ ] Failed deliveries are recorded and retry behavior is bounded.
- [ ] Email logs avoid storing full sensitive message bodies unnecessarily.
- [ ] Templates are centralized, localized if needed, and escape untrusted values.
- [ ] Links use correct public base URL and signed/expiring tokens when appropriate.
- [ ] Unsubscribe/preferences and legal requirements are respected for marketing mail.
- [ ] Notification preferences, deduplication, and user consent are handled where relevant.
- [ ] Rate limits prevent email bombing and abuse of verification/reset flows.
- [ ] Delivery provider webhooks are signature-verified.

Do not send an email during audit unless the user explicitly asks.
