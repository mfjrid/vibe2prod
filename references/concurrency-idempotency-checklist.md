# Concurrency, Race Conditions & Idempotency Checklist

Audit state mutations, financial flows, shared resources, and asynchronous events to guarantee that concurrent operations cannot corrupt data, bypass business rules, or duplicate transactions.

---

## 1. Race Conditions & Concurrency Controls

### Read-Modify-Write & TOCTOU (Time-of-Check to Time-of-Use)
- [ ] **Atomic Numerical Updates**: Balance deductions, token/credit usage, inventory counts, and vote tallies use atomic database queries (e.g., `UPDATE accounts SET balance = balance - :amount WHERE id = :id AND balance >= :amount RETURNING balance`) rather than fetching the current value into application memory, computing the new value, and saving it back.
- [ ] **Voucher & Promo Code Single-Use**: Coupon redemption and one-time invitation flows verify and mark the code as consumed within an atomic operation or lock, preventing parallel requests from redeeming the same code multiple times.
- [ ] **Quota & Rate Cap Enforcement**: Strict resource quotas (e.g., AI token limits, monthly plan caps) cannot be exceeded by firing multiple simultaneous requests in parallel.
- [ ] **Inventory & Booking Oversell Protection**: Seats, tickets, hotel rooms, or physical stock cannot be reserved beyond availability by simultaneous checkout attempts.

### Database-Level Locking & Isolation
- [ ] **Pessimistic Locking Where Necessary**: Operations requiring multi-statement reads and writes (e.g., transferring funds between accounts, multi-item checkout) use `SELECT ... FOR UPDATE` (or `FOR NO KEY UPDATE` in PostgreSQL) inside explicit transaction blocks (`BEGIN ... COMMIT`).
- [ ] **Lock Ordering & Deadlock Prevention**: When locking multiple rows across tables or records, locks are acquired in a consistent, deterministic order (e.g., sorting row IDs ascending before locking) to prevent database deadlocks.
- [ ] **Short Lock Durations**: Transactions holding database row or table locks contain only database operations. Never perform network calls, third-party API requests, file uploads, or slow hashing while holding an open database transaction lock.
- [ ] **Optimistic Concurrency Control (OCC)**: For high-read, low-write resources (e.g., document editing, status updates), verify version or timestamp checks exist (`UPDATE documents SET ..., version = version + 1 WHERE id = :id AND version = :old_version`) and report 409 Conflict if stale.

### Uniqueness & Duplicate Creation Races
- [ ] **Database-Level Unique Constraints**: Uniqueness is enforced by database `UNIQUE` indexes (e.g., unique email, unique slug per tenant, unique `(user_id, item_id)`), not solely by application-level existence checks (`if (!findFirst()) create()`).
- [ ] **Graceful Conflict Handling**: Unique constraint violations are caught gracefully using upserts (`ON CONFLICT DO NOTHING / UPDATE`, `INSERT ... ON DUPLICATE KEY UPDATE`) or translated into clean 409 Conflict responses rather than unhandled 500 crashes.

### Multi-Step Transactional Integrity
- [ ] **All-or-Nothing Transactions**: Multi-table state mutations (e.g., creating an order + decrementing inventory + logging ledger transaction) are wrapped in atomic database transactions (`tx.begin() ... tx.commit()`).
- [ ] **Explicit Rollback on Error**: Unhandled exceptions inside transaction blocks trigger immediate and clean rollbacks without leaving partial writes or orphaned records.

### Distributed Locks & Shared Memory Hazards
- [ ] **Distributed Mutex for Multi-Instance Deployments**: When application logic coordinates state across external APIs and databases in clustered or serverless deployments, use distributed locks (Redis Redlock, PostgreSQL advisory locks `pg_advisory_xact_lock`) with mandatory TTLs to prevent abandoned locks.
- [ ] **No Cross-Request Mutable Memory**: In Node.js, Python, or long-running daemon workers, verify that module-level singletons or global variables do not store mutable per-request state that could bleed between concurrent async requests.
- [ ] **Atomic File Writes**: Local file uploads and disk writes write to a unique temporary file first and rename atomically (`fs.rename`, `os.replace`), preventing concurrent processes from reading half-written files.

---

## 2. Idempotency & Replay Protection

### State-Mutating APIs (POST / PUT / PATCH / DELETE)
- [ ] **Idempotency-Key Support**: Critical state-changing endpoints (payments, checkout, transfers, subscription changes, refunds) accept an `Idempotency-Key` or `X-Idempotency-Key` HTTP header.
- [ ] **Atomic Key Storage & Status Tracking**: Idempotency keys are recorded in a persistent store (database or Redis) with status (`in_progress`, `completed`, `failed`), request payload hash, and the cached response payload.
- [ ] **In-Flight Duplicate Handling**: If a duplicate request arrives with the same key while the first request is still `in_progress`, the API returns HTTP 409 Conflict or waits briefly rather than executing a duplicate operation.
- [ ] **Payload Hash Verification**: If a subsequent request arrives with a previously used idempotency key but a different request body, the server rejects it with HTTP 422 Unprocessable Entity or 400 Bad Request to prevent key recycling across different operations.
- [ ] **Cached Response Replay**: Repeated requests with the exact same key and payload return the original cached response with a header indicating replay (e.g., `Idempotent-Replayed: true`).
- [ ] **Idempotency Key TTL**: Idempotency records have a safe retention window (e.g., 24 to 72 hours) and automated cleanup.

### Webhook Ingestion & Deduplication
- [ ] **At-Least-Once Delivery Resilience**: All incoming webhook receivers (Stripe, PayPal, GitHub, Shopify, Midtrans, Xendit, Resend) are resilient to duplicate event deliveries.
- [ ] **Persistent Webhook Event Deduplication**: Webhooks record external event identifiers (e.g., `event_id`, `delivery_id`) into a dedicated `webhook_events` table or Redis store with a `UNIQUE` constraint before or during processing.
- [ ] **Duplicate Webhook Short-Circuit**: If a webhook event ID has already been recorded and processed, the handler immediately returns HTTP 200 OK without re-running side effects (no duplicate account credits, duplicate order creation, or repeated email notifications).
- [ ] **Signature & Timestamp Verification**: Webhook endpoints verify HMAC signatures and reject payloads with timestamps older than tolerance limits (e.g., > 5 minutes) to protect against replay attacks.

### Background Queues & Worker Jobs
- [ ] **Idempotent Job Handlers**: Background workers (BullMQ, Celery, Laravel Queue, Sidekiq, Inngest) are designed so that executing a job multiple times with identical parameters yields the exact same final state.
- [ ] **Queue Deduplication Keys**: Jobs dispatched for unique events utilize deduplication locks or job IDs (e.g., `jobId` in BullMQ, `ShouldBeUnique` in Laravel) to prevent duplicate jobs from queueing during burst events.
- [ ] **State Machine Guardrails**: Job mutations check state preconditions before applying transitions (e.g., `WHERE status = 'PENDING'`), ignoring or logging jobs if the record has already transitioned to `'PROCESSED'` or `'CANCELLED'`.

### Outbound Third-Party API Calls
- [ ] **Outbound Idempotency Keys**: Requests made to third-party payment gateways, messaging providers, or financial APIs generate and send unique idempotency keys so that network retries do not trigger duplicate external charges.

### Frontend Double-Submission Safeguards
- [ ] **Client-Side Rapid Submission Prevention**: Buttons performing mutating actions (checkout, transfer, submit) are disabled upon initial click, show pending indicators, and prevent accidental double-clicks or rapid keyboard submits. Client-side safeguards complement, but never replace, server-side idempotency.

