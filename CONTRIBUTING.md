# Contributing to vibe2prod

Thank you for considering contributing to `vibe2prod`! We welcome contributions from engineers, security researchers, and developers of all backgrounds.

---

## Areas Where You Can Help

1. **Framework Adapters (`adapters/`)**
   - We want deep, actionable discovery guidance for major frameworks.
   - High-priority additions: Ruby on Rails, Django, Go (Fiber/Gin), SvelteKit, Spring Boot, Phoenix (Elixir).

2. **Reference Checklists (`references/`)**
   - High-impact industry checklists (e.g., OWASP Top 10, SOC2 common controls, HIPAA/GDPR data protection baselines).

3. **Remediation Templates (`templates/`)**
   - New AI prompt recipes for common vulnerability classes and architectural fixes.

---

## Guidelines for New Adapters

When creating a new adapter in `adapters/<framework>.md`:
1. **Primary Inspection Targets**: List the key configuration files, routing mechanisms, ORM/DB setup, and middleware files.
2. **Critical Audit Checklist**: Provide concise, actionable bullet points focused on security, data access, auth, performance, and operational hygiene.
3. **Evidence-First**: Remind the agent to inspect concrete symbols and configurations, avoiding broad generalizations.

---

## Submitting a Pull Request

1. Fork the repository.
2. Create your feature branch (`git checkout -b feature/new-adapter-django`).
3. Commit your changes with a clear message.
4. Push to the branch (`git push origin feature/new-adapter-django`).
5. Open a Pull Request with a summary of what you added and why it benefits production readiness.

---

## License

By contributing to `vibe2prod`, you agree that your contributions will be licensed under the [MIT License](LICENSE).

