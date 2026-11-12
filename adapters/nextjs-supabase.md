# Next.js & Supabase Adapter

Audit guidance for applications built with Next.js (App Router or Pages Router) and Supabase or similar Backend-as-a-Service (BaaS) architectures. This stack is standard among vibe-coders and rapid-prototypers, but frequently contains critical authorization, secret exposure, and data integrity gaps.

## Primary Inspection Targets

1. **Manifests & Config**:
   - `package.json`, `next.config.js` / `next.config.mjs`, `tsconfig.json`.
   - `.env.example`, environment variable declarations, and runtime config.
   - Supabase config: `supabase/config.toml`, migrations under `supabase/migrations/`.

2. **Routing & Server Boundaries**:
   - App Router: `app/**/page.tsx`, `layout.tsx`, `route.ts` (API routes), Server Actions (`"use server"`).
   - Pages Router: `pages/api/**/*.ts`, `getServerSideProps`.
   - Middleware: `middleware.ts` or `src/middleware.ts`.

3. **Client vs Server Code Boundaries**:
   - Inspect files marked with `"use client"`.
   - Check if database queries, admin SDKs, or secret keys are imported into client bundles.

4. **Data Access & Supabase Client Initialization**:
   - Supabase client helpers: `@supabase/ssr`, `@supabase/supabase-js`, `@supabase/auth-helpers-nextjs`.
   - Separate initialization for browser client (`createBrowserClient`), server client (`createServerClient`), and admin/service role client.

---

## Critical Audit Checklist

### 1. Secret Exposure & Environment Variables
- [ ] **No Service Role Key in Client Bundle**: Verify that `SUPABASE_SERVICE_ROLE_KEY` or any admin token is NEVER prefixed with `NEXT_PUBLIC_` and never imported into components with `"use client"`.
- [ ] **Client Key Scope**: Confirm `NEXT_PUBLIC_SUPABASE_ANON_KEY` is only used where Row Level Security (RLS) is strictly enforced.
- [ ] **Third-Party API Secrets**: Check that Stripe secret keys, OpenAI/Gemini API keys, Resend/SendGrid keys, and database passwords are only accessed in server-only modules (`server-only` package or server routes).

### 2. Supabase Row Level Security (RLS) & Policies
- [ ] **RLS Enabled on ALL Tables**: Ensure `ALTER TABLE <table_name> ENABLE ROW LEVEL SECURITY;` is present in migrations for every public table.
- [ ] **Default Deny**: Confirm tables without explicit policies do not accidentally expose data to the `anon` or `authenticated` roles.
- [ ] **BOLA/IDOR Prevention in Policies**: Verify policies check `auth.uid() = user_id` or tenant ID, rather than trusting client-supplied IDs in the payload.
- [ ] **Bypass Risk**: Check where `service_role` client is used in Server Actions or Route Handlers; verify it performs explicit authorization checks before running queries.

### 3. Server Actions & Route Handlers Authorization
- [ ] **Server Actions Are Public Endpoints**: Every Server Action (`"use server"`) can be invoked directly via HTTP POST by any client. Verify each Server Action performs its own session and permission checks.
- [ ] **Input Validation**: Server Actions and API routes must validate input with a runtime schema validator (e.g., `zod`) rather than relying on TypeScript types alone.
- [ ] **CSRF & Origin Checking**: Verify Next.js Server Actions origin headers are respected and cookies use `SameSite=Lax` or `Strict`.
- [ ] **Double-Submission & Idempotency**: Verify mutating Server Actions (checkout, payment, order creation) prevent double submissions via pending state hooks (`useActionState`, `useFormStatus`) and enforce server-side idempotency keys.

### 4. Concurrency, Race Conditions & Webhooks
- [ ] **Atomic Updates via Supabase RPC**: Look for read-modify-write patterns (e.g. `select('credits')` followed by `update({ credits: credits - 1 })`). These MUST be replaced with PostgreSQL atomic queries or stored procedures (RPC) using `UPDATE ... SET credits = credits - 1 WHERE id = ... AND credits >= 1` or `SELECT ... FOR UPDATE`.
- [ ] **Webhook Event Deduplication**: Route Handlers receiving webhooks (Stripe, LemonSqueezy, Clerk, Resend) must log `event.id` into a dedicated `webhook_events` table with a `UNIQUE` index. Duplicate webhook retries must return `200 OK` without re-crediting or re-fulfilling.
- [ ] **Interactive Transactions**: In Prisma or Drizzle, ensure multi-record mutations are wrapped in interactive transactions (`$transaction(async (tx) => ...)`) rather than isolated sequential calls.

### 5. Middleware & Route Protection
- [ ] **Middleware Is Not Complete Security**: Next.js Middleware runs on the Edge runtime and can be bypassed if static assets or prefetch routes are misconfigured. Ensure page-level and action-level checks exist behind middleware redirects.
- [ ] **Session Refreshing**: Verify middleware uses `@supabase/ssr` to properly refresh expired auth tokens on request.

### 6. Storage Buckets & File Uploads
- [ ] **Bucket Privacy**: Check whether Supabase Storage buckets are marked `public` or `private`. Private files (invoices, ID documents) must use private buckets with signed URLs.
- [ ] **Storage RLS Policies**: Check storage policies on `storage.objects` to ensure users cannot overwrite or read other users' uploaded files.
- [ ] **File Size & MIME Limits**: Ensure client and server enforce upload size limits and content-type validation.

### 7. Performance, Hydration & Database Best Practices
- [ ] **Database Indexes**: Check migrations for indexes on `user_id`, foreign keys, and columns used in `WHERE`, `ORDER BY`, or join clauses.
- [ ] **Connection Pooling**: When connecting directly via Prisma/Drizzle or Supabase pooler (Supavisor), verify connection pool limits are configured for serverless/edge environments (`pgbouncer=true` or port `6543`).
- [ ] **Waterfalls**: Look for nested `await` calls in server components that could run in parallel via `Promise.all()`.
- [ ] **Cache Poisoning / Revalidation**: Check `revalidatePath` and `revalidateTag` usage; ensure sensitive user-specific data is marked with `export const dynamic = 'force-dynamic'` or `noStore()` to avoid caching across users.

