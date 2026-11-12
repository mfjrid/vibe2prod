# AI Remediation Prompt Recipes (Auto-Fix for Vibecoders)

When auditing an application built with AI coding assistants (Cursor, Claude Code, Antigravity, Windsurf, Bolt), generate copy-pasteable prompt recipes for each critical finding. Vibecoders can paste these directly into their AI agent to implement enterprise-grade fixes safely without causing regressions.

---

## Standard Prompt Template for Findings

When reporting a finding, provide the remediation prompt in this structure:

```markdown
### 🤖 AI Agent Fix Prompt for Finding [{FINDING_ID}]: {FINDING_TITLE}

Copy and paste this prompt directly into your AI coding tool (Cursor / Claude Code / Antigravity):

---
You are an expert senior software engineer tasked with fixing a specific production readiness issue in this codebase.

**Task Context**:
- Issue ID: {FINDING_ID}
- Issue Description: {FINDING_DESCRIPTION}
- Target Files: `{FILE_PATHS}`
- Severity: {SEVERITY}

**Objective**:
{OBJECTIVE_SUMMARY}

**Strict Implementation Constraints**:
1. Do not modify unrelated files or change business logic outside this issue.
2. Follow existing code style, naming conventions, and project architecture.
3. Use safe defaults and handle all edge cases (null values, unauthenticated states, error conditions).
4. Do not delete or downgrade existing tests.
5. Provide automated tests or verification commands to prove the fix works.

**Step-by-Step Instructions**:
1. Inspect `{PRIMARY_FILE}` at `{CODE_SYMBOL_OR_LINE}`.
2. {STEP_2_INSTRUCTION}
3. {STEP_3_INSTRUCTION}
4. Run tests or build checks to confirm no regressions: `{TEST_COMMAND}`.
---
```

---

## Ready-to-Use Prompt Recipes for Common Findings

### Recipe 1: Fixing Broken Object-Level Authorization (IDOR / BOLA)

```markdown
You are an expert security engineer. We need to fix an IDOR vulnerability where users can access or mutate resources belonging to other users.

**Target**: `{ROUTE_OR_CONTROLLER_PATH}`
**Issue**: The endpoint fetches/updates records by ID without verifying the record's owner matches the authenticated user.

**Instructions**:
1. In `{ROUTE_OR_CONTROLLER_PATH}`, retrieve the currently authenticated user session or token.
2. Before updating or returning the record, verify that `record.user_id === session.user.id` (or the equivalent tenant/organization check).
3. If unauthorized, immediately return a 403 Forbidden (or 404 Not Found) response with no sensitive data.
4. Add a unit/integration test verifying that user A cannot access user B's resource.
```

### Recipe 2: Adding Runtime Input Validation with Zod / Pydantic

```markdown
You are an expert backend engineer. We need to add strict server-side runtime validation to prevent invalid data or injection attacks.

**Target**: `{ENDPOINT_OR_ACTION_PATH}`
**Issue**: Request payload is used directly without runtime schema validation.

**Instructions**:
1. Define a strict schema using `{ZOD_OR_PYDANTIC}` for the payload.
2. Set explicit string length limits, valid email formats, and number bounds.
3. Validate the incoming request before any database query or processing logic runs.
4. If validation fails, return a 400 Bad Request with clear field-level validation errors.
5. Ensure no unexpected extra fields are accepted.
```

### Recipe 3: Securing Next.js Server Actions & Environment Variables

```markdown
You are a senior Next.js security specialist. We need to secure Server Actions and prevent secret leakage.

**Target**: `{ACTION_FILE_PATH}`
**Issue**: Server action is exported without session validation, or secrets are exposed via `NEXT_PUBLIC_`.

**Instructions**:
1. Check all environment variables in this file. Remove any `NEXT_PUBLIC_` prefixes from private API keys, service role tokens, or database credentials.
2. In each exported `"use server"` function, invoke the server session helper first.
3. Reject unauthenticated requests immediately with an error or redirect.
4. Wrap execution in a `try...catch` block. Log the detailed error on the server console, but return a safe generic message to the client.
```

### Recipe 4: AI Endpoint Cost Protection & Rate Limiting

```markdown
You are an AI infrastructure engineer. We need to protect an expensive LLM endpoint from cost runaway and abuse.

**Target**: `{AI_ENDPOINT_PATH}`
**Issue**: The endpoint calls an external LLM without rate limits, max token caps, or abort signal handling.

**Instructions**:
1. Add rate limiting middleware (e.g. max 10 requests per minute per IP / authenticated user).
2. Ensure the LLM API call has `max_tokens` (or `max_completion_tokens`) explicitly set.
3. If streaming responses, connect the request `AbortSignal` to cancel the upstream model call if the client disconnects.
4. Enforce a maximum character limit on the input prompt before calling the model.
```

### Recipe 5: Health Check & Graceful Shutdown

```markdown
You are a DevOps and reliability engineer. We need to add a production health check and graceful shutdown handler.

**Target**: Server bootstrap / `{MAIN_SERVER_FILE}`
**Issue**: Missing health check endpoint and no graceful exit handling for deployment restarts.

**Instructions**:
1. Add a `GET /healthz` endpoint that tests connectivity to the database and returns `{"status": "ok"}` with HTTP 200, or HTTP 503 if the database is unreachable.
2. Add listeners for `SIGTERM` and `SIGINT` signals.
3. When triggered, stop accepting new connections, finish existing requests within a 15-second timeout, close database pools, and exit cleanly with code 0.
```

### Recipe 6: Fixing Race Conditions & TOCTOU (Atomic DB Updates & Locking)

```markdown
You are an expert backend engineer specializing in high-concurrency systems and transactional integrity. We need to eliminate a race condition (Time-of-Check to Time-of-Use / TOCTOU) in a critical mutation path.

**Target**: `{TARGET_FILE_OR_ROUTE}`
**Issue**: The code reads a record into application memory to verify conditions (e.g. balance, inventory stock, promo code usage) and then updates the record in a separate step. Under concurrent requests, this allows double-spending, inventory overselling, or multiple redemptions.

**Instructions**:
1. Inspect `{TARGET_FILE_OR_ROUTE}` where `{MUTATION_FUNCTION}` performs the check-and-update.
2. Refactor the logic using one of these two battle-tested approaches appropriate for this stack ({FRAMEWORK_OR_ORM}):
   - **Approach A (Atomic SQL Update with Row Count Check - Preferred)**:
     Replace the check + update with a single atomic conditional query:
     `UPDATE {TABLE} SET {COLUMN} = {COLUMN} - :amount WHERE id = :id AND {COLUMN} >= :amount RETURNING *;`
     If 0 rows are affected, immediately return a 409 Conflict / 400 Bad Request indicating insufficient balance/stock.
   - **Approach B (Explicit Transaction with Pessimistic Locking)**:
     Wrap the operation in a database transaction (`BEGIN ... COMMIT`).
     Select the target record using `SELECT ... FOR UPDATE` (or ORM equivalent: `.with_for_update()`, interactive transaction lock), perform the check, execute the update, and commit.
3. Keep the locked transaction window as narrow as possible. Do NOT execute external HTTP calls, email sending, or slow hashing while holding the database lock.
4. Add a concurrency unit or integration test simulating 5–10 parallel requests to confirm that only the valid number of requests succeed and all others fail gracefully without data corruption.
```

### Recipe 7: Implementing API Idempotency & Webhook Deduplication

```markdown
You are a senior backend and payment systems engineer. We need to implement strict idempotency and deduplication to prevent duplicate transactions on network retries and double-clicks.

**Target**: `{TARGET_FILE_OR_WEBHOOK_ROUTE}`
**Issue**: Mutation requests or incoming webhooks can be delivered more than once (network retries, user double-clicks, or at-least-once webhook delivery), causing duplicate charges, fulfillment, or database records.

**Instructions**:
1. For **API Mutation Endpoints (e.g. checkout, payment, transfer)**:
   - Extract the `Idempotency-Key` or `X-Idempotency-Key` header from the incoming request.
   - In your database or Redis, check if the key already exists:
     - If status is `in_progress`: return 409 Conflict (request currently processing).
     - If status is `completed`: return the cached response payload with an `Idempotent-Replayed: true` header immediately without re-executing logic.
   - If new, atomically record the key with status `in_progress` and request hash. Execute the mutation in a transaction, store the final response payload, update status to `completed`, and set a 24-72 hour TTL.
2. For **Incoming Webhook Handlers (e.g. Stripe, GitHub, Payment Gateway)**:
   - Create or use a `webhook_events` table with schema: `event_id VARCHAR PRIMARY KEY`, `provider VARCHAR`, `event_type VARCHAR`, `created_at TIMESTAMP`.
   - Before executing business logic, attempt to atomically insert `event_id`.
   - If the insert fails on unique constraint violation (or event already exists), return HTTP 200 OK immediately and log: "Duplicate webhook event {event_id} ignored."
   - If new, execute the fulfillment logic inside a transaction and commit.
3. Add tests verifying that sending the exact same payload twice results in only one side effect.
```

