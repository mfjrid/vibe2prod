# vibe2prod 🚀

> **Production Readiness, Security Hardening & Pre-Release Audit for Web Applications.**  
> *From rapid AI prototypes to mission-critical enterprise systems.*

[![Agent Skill](https://img.shields.io/badge/Agent_Skill-vibe2prod-blueviolet?style=for-the-badge)](https://github.com/mfjrid/vibe2prod)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
[![Compatible With](https://img.shields.io/badge/Compatible_With-Antigravity%20|%20Claude%20Code%20|%20Cursor%20|%20Codex-orange?style=for-the-badge)](#install)

---

## The vibe2prod Philosophy

In modern software development:
- **"Vibe"** represents rapid innovation, intuition, and AI-accelerated momentum.
- **"Prod"** represents non-negotiable enterprise resilience: zero-trust authorization, uncompromised security, verified disaster recovery, observability, and cost control.

Whether you are launching a prototype built in an afternoon or reviewing a mature enterprise system with years of production history, **`vibe2prod` provides an objective, evidence-based Production Readiness Review (PRR).** It discovers what your application actually does, verifies it against rigorous checklists, highlights operational gaps, and recommends concrete solutions.

---

## Who Is This For?

### 🏢 1. Enterprise Teams & Engineering Leads
- **Pre-Release Verification**: Run comprehensive quality and security audits before major deployments, client handovers, or compliance evaluations.
- **Architectural Drift Detection**: Identify forgotten diagnostic endpoints, unindexed database tables, missing rate limiters, or misconfigured reverse proxies.
- **Zero-Risk Read-Only Audit**: The skill never executes destructive operations, migrates databases, or discloses production secrets.

### 🚀 2. Startups & Scaleups
- **Hardening for Scale**: Ensure your database connection pooling handles traffic spikes, file uploads are resource-bounded, and server processes exit gracefully.
- **Financial & Abuse Defense**: Prevent denial-of-wallet attacks by enforcing per-user quotas, max token bounds, and rate limits on expensive API/AI endpoints.

### 🤖 3. AI Builders & Modern Developers
- **Catch AI Blind Spots**: AI assistants (Cursor, Claude Code, v0, Bolt, Windsurf) excel at functional features but frequently omit server-side auth, object-level checks (BOLA/IDOR), and database indexes.
- **Auto-Fix Prompt Recipes**: Generates copy-pasteable prompts (`templates/remediation-prompts.md`) to let your AI coding tool implement fixes safely with automated tests.

---

## Core Capabilities

- 🔍 **Evidence-Based Discovery**: Every finding cites specific files, routes, ORM models, or configuration keys. No guesswork or superficial assumptions.
- ⚡ **Concurrency & Idempotency Hardening**: Detects dangerous read-modify-write (TOCTOU) race conditions, balance overdrafts, missing database locks/transactions, API double-click vulnerabilities, and un-deduplicated webhook replays.
- 🛡️ **Comprehensive Checklist Suite**: Covers security, concurrency/race conditions, idempotency, access management, backup & restore verification, logging, performance, privacy/accessibility, SEO, and AI/LLM cost protection.
- 📊 **Enterprise Readiness Scoring**: Computes a weighted 0–100 readiness score with strict severity caps (e.g., any unresolved P0 vulnerability caps the overall score at 49).
- 🔌 **Deep Framework Adapters**: Specialized discovery heuristics for Next.js (App Router/Server Actions), Supabase, Python FastAPI, AI/LLM applications, Node.js, Laravel, WordPress, and generic web stacks.
- 🤖 **AI Auto-Fix Prompt Generator**: Bridges the gap between audit findings and actual code remediation.

---

## Directory Structure

```text
vibe2prod/
├── SKILL.md                          # Core agent instructions and workflow
├── adapters/                         # Stack-specific discovery adapters
│   ├── nextjs-supabase.md            # Next.js App Router, Server Actions, Supabase RLS
│   ├── python-fastapi.md             # FastAPI, Pydantic, async DB concurrency
│   ├── ai-llm-apps.md                # OpenAI/Claude/Gemini APIs, rate limits, cost caps
│   ├── nodejs.md                     # Express, Fastify, Node backends
│   ├── laravel.md                    # Laravel ecosystem & Eloquent
│   ├── wordpress.md                  # WordPress plugins & themes
│   ├── php-native.md                 # Vanilla PHP applications
│   └── generic-web.md                # Framework-agnostic baseline
├── references/                       # In-depth production checklists
│   ├── vibecoder-pitfalls-checklist.md  # Traps in rapid & AI-assisted codebases
│   ├── concurrency-idempotency-checklist.md # Race conditions, atomic locks, idempotency
│   ├── security-checklist.md            # Auth, CSRF, CORS, injection, headers
│   ├── access-management-checklist.md   # RBAC, tenant isolation, session security
│   ├── backup-recovery-checklist.md     # Automated backup & restore verification
│   ├── logging-observability-checklist.md # Error tracking, logs, request tracing
│   ├── performance-checklist.md         # DB indexes, waterfalls, cache headers
│   ├── maintenance-diagnostics-checklist.md # Health checks, graceful shutdowns
│   ├── privacy-accessibility-checklist.md # PII handling, WCAG compliance
│   ├── email-notification-checklist.md  # Deliverability, SPF/DKIM, rate limits
│   ├── seo-checklist.md                 # Meta tags, sitemaps, robots.txt
│   └── testing-delivery-checklist.md    # CI/CD, unit tests, lockfiles
└── templates/                        # Actionable output templates
    ├── audit-report.md               # Executive summary & categorized findings (Markdown)
    ├── audit-report.html             # Interactive visual dashboard report (Single-file HTML)
    ├── remediation-prompts.md        # Copy-paste AI prompt recipes for auto-fixing
    ├── feature-inventory.md          # Control-by-control evidence table
    └── implementation-plan.md        # Risk-prioritized remediation roadmap
```

---

## Install

Clone or copy `vibe2prod` into your AI coding environment's skills directory:

### For Antigravity
```bash
git clone https://github.com/mfjrid/vibe2prod.git ~/.gemini/antigravity/skills/vibe2prod
```

### For Claude Code
```bash
git clone https://github.com/mfjrid/vibe2prod.git ~/.claude/skills/vibe2prod
# Or in a project directory:
git clone https://github.com/mfjrid/vibe2prod.git .claude/skills/vibe2prod
```

### For Cursor / Codex / OpenCode
```bash
git clone https://github.com/mfjrid/vibe2prod.git .agents/skills/vibe2prod
```

> **Note**: Keep `SKILL.md`, `adapters/`, `references/`, and `templates/` together in the same directory.

---

## Usage Examples

Invoke the skill directly in conversation with your agent:

### 1. Enterprise Pre-Release Audit (Read-Only)
```text
Audit this project using vibe2prod. Audit only, do not modify code.
```
*Bahasa Indonesia:*
```text
Lakukan audit standar kesiapan produksi pada aplikasi ini menggunakan vibe2prod. Mode audit saja, jangan ubah file.
```

### 2. Focused Security & Hardening Review
```text
Run vibe2prod focusing on access management, security, and database resilience.
```

### 3. Generate AI Fix Prompts
```text
Based on the audit findings, generate AI remediation prompts from templates/remediation-prompts.md for all P0 and P1 findings.
```

### 4. Implement an Approved Remediation Scope
```text
Implement only findings SEC-002 and PERF-001. Follow the remediation plan, write tests, and verify results.
```

---

## Operating Workflow

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│    Phase 1: AUDIT      │ ──> │  Phase 2: PLAN/PROMPTS │ ──> │   Phase 3: IMPLEMENT   │
│ (Strict Read-Only PRR) │     │ (Readiness Score & Fix)│     │(Small, Verified Steps) │
└────────────────────────┘     └────────────────────────┘     └────────────────────────┘
```

1. **Audit Mode (Default)**: Deep inspection of routes, models, middleware, and config. Maps findings into `IMPLEMENTED`, `PARTIAL`, `MISSING`, or `UNVERIFIED`.
2. **Plan & Prompts Mode**: Ranks findings by severity (`P0 CRITICAL` down to `P3 LOW`) and outputs copy-pasteable AI fix prompts.
3. **Implement Mode**: Executed only after explicit approval on a bounded scope, writing tests and verifying behavior.

---

## Contributing

Contributions are welcome! If you'd like to add new framework adapters (e.g., Ruby on Rails, Django, Go Fiber, Spring Boot) or new audit checklists, please check out [CONTRIBUTING.md](CONTRIBUTING.md) and open a Pull Request.

---

## License

[MIT License](LICENSE) © 2026 Fajar & Contributors. Free for personal, agency, and commercial use.
