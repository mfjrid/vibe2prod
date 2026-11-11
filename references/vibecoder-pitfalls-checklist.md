# Vibecoder Common Pitfalls Checklist

This checklist focuses on the subtle, critical gaps commonly found in applications built rapidly with AI coding assistants (Cursor, v0, Bolt, Claude Code, Windsurf, Lovable). AI agents excel at generating functional feature prototypes, but frequently omit foundational enterprise practices unless explicitly instructed.

Audit against these common pitfalls when reviewing applications transitioning from vibe-coded prototypes to enterprise production.

---

## 1. Secrets & Environment Exposure
- [ ] **Frontend Secret Leakage**: No private keys or service role secrets exposed via client-side environment prefixes (`NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, `EXPO_PUBLIC_`).
- [ ] **Git History Cleanliness**: `.env`, `.env.local`, `.pem`, and service account JSONs are excluded in `.gitignore` and have never been committed in git history.
- [ ] **Runtime Environment Validation**: Required environment variables are validated at application bootstrap using schema libraries (e.g., `zod`, `envalid`, `pydantic-settings`) rather than failing silently with `undefined`.

## 2. Authorization vs Authentication (BOLA / IDOR)
- [ ] **Object-Level Authorization**: Routes fetching, updating, or deleting by ID (e.g., `/api/documents/[id]`) explicitly verify that the requesting user owns the object or belongs to the correct tenant.
- [ ] **Server Action Route Protection**: In Next.js/Remix, every Server Action verifies user session and roles internally. AI often treats Server Actions as secure internal functions, ignoring that they are publicly accessible HTTP POST endpoints.
- [ ] **Default-Deny Role Enforcement**: Admin panels and privileged routes cannot be accessed simply by knowing the URL or manipulating frontend routing state.

## 3. Database Integrity & Performance
- [ ] **Foreign Key Indexes**: Every foreign key column (`user_id`, `org_id`, `team_id`) has an explicit database index. AI frequently creates relations without defining indexes, causing queries to stall under production loads.
- [ ] **Connection Pooling in Serverless**: Serverless and edge environments (Vercel, Netlify, Cloudflare Workers, AWS Lambda) use connection pooling (e.g. Supavisor, Prisma Accelerate, PgBouncer) to avoid exhausting PostgreSQL connections.
- [ ] **Schema Versioning**: Database changes are tracked in deterministic migration files (Prisma, Drizzle, Alembic, Supabase migrations) rather than manual schema edits made directly in cloud dashboards.
- [ ] **N+1 Query Prevention**: ORM queries in loops or nested components use eager loading, joins, or dataloaders instead of firing individual database queries for each row.

## 4. Input Validation & Request Size Limits
- [ ] **Server-Side Validation**: All incoming request bodies, query params, and headers are validated against strict runtime schemas (Zod, Pydantic, Joi). Typescript types alone provide zero runtime protection.
- [ ] **Unbounded Request Payloads**: File uploads and JSON request bodies have explicit size limits configured (e.g., max 5MB for uploads, max 1MB for JSON) to prevent memory exhaustion DoS.
- [ ] **File Type Magic Byte Verification**: Upload endpoints verify file content via MIME magic bytes, not just untrusted user-supplied file extensions (`.jpg`, `.png`).

## 5. Error Handling & Observability
- [ ] **No Swallowed Exceptions**: Codebase is free of empty `catch (err) {}` or `catch (err) { console.log(err) }` blocks that swallow failures and return empty `200 OK` responses.
- [ ] **Centralized Error Monitoring**: Sentry, Datadog, Highlight, or similar error tracking is integrated in both frontend and backend to capture runtime crashes with stack traces.
- [ ] **Safe Production Errors**: Errors sent to the browser/client do not contain SQL syntax, database connection strings, file paths, or internal stack traces.

## 6. Lifecycle, Deployments & Health Checks
- [ ] **Health Check Endpoint**: Dedicated `/healthz` or `/api/health` endpoint exists to verify server status and basic database connectivity for load balancers and container orchestrators.
- [ ] **Graceful Shutdown**: Node.js and Python processes listen to `SIGTERM` and `SIGINT` signals, completing in-flight requests before exiting.
- [ ] **Hardcoded URLs**: No hardcoded `http://localhost:3000` or development URLs in redirect handlers, webhook listeners, or CORS configurations.

## 7. AI & Cost runaway Safeguards
- [ ] **AI Endpoint Rate Limiting**: Any endpoint calling LLMs (OpenAI, Anthropic, Gemini) is heavily rate-limited and protected behind user authentication.
- [ ] **Max Token Caps**: All LLM completion calls have strict `max_tokens` configured.
- [ ] **Stream Abort Handling**: Serverless and streaming functions abort LLM generation when the client disconnects or aborts the request.

## 8. Backup & Data Retention
- [ ] **Automated Backup Schedule**: Database has automated daily backups with a verified point-in-time recovery (PITR) strategy.
- [ ] **Soft Deletes or Audit Logs**: High-value records (users, orders, financial records) utilize soft deletion (`deleted_at`) or change data capture to protect against accidental user or admin deletion.

