# Python & FastAPI Adapter

Audit guidance for Python web applications, specifically FastAPI, Flask, and modern async Python APIs commonly paired with frontend frameworks or AI services.

## Primary Inspection Targets

1. **Manifests & Dependencies**:
   - `pyproject.toml`, `requirements.txt`, `Pipfile`, `poetry.lock`.
   - Check framework versions (FastAPI, Starlette, Pydantic, SQLAlchemy, Alembic, Uvicorn).
   - Python version compatibility and security advisory status.

2. **Application Factory & Middleware**:
   - Main entry point: `main.py`, `app.py`, `app/main.py`.
   - CORS middleware: `CORSMiddleware` configuration (`allow_origins`, `allow_credentials`).
   - Trusted host middleware and proxy headers (`ProxyHeadersMiddleware`, Forwarded headers).
   - Rate limiting middleware (e.g. `slowapi`).

3. **Data Schemas & Input Validation**:
   - Pydantic models (`schemas/` or `models/`).
   - Strict typing, field constraints (e.g., `Field(max_length=...)`), and extra field handling (`extra = "forbid"`).
   - SQL injection risk in raw SQL queries vs ORM queries (SQLAlchemy, SQLModel, Tortoise).

4. **Authentication & Authorization**:
   - Dependency injection security: `Depends(get_current_user)`, `OAuth2PasswordBearer`, HTTPBearer.
   - JWT token generation, signature algorithms (e.g., HS256 vs RS256), expiration time (`exp`), and secret key management.
   - Password hashing: passlib / bcrypt / argon2.

5. **Database & Asynchronous Concurrency**:
   - Database connection pooling settings (`pool_size`, `max_overflow`, `pool_recycle`, `pool_pre_ping`).
   - Async session lifecycle: ensuring database sessions are properly closed using async context managers or FastAPI `Depends` yield patterns.
   - Alembic migration scripts (`alembic/versions/`).

---

## Critical Audit Checklist

### 1. Request Validation & Schemas
- [ ] **Runtime Validation**: Ensure every route parameter and request body uses Pydantic schemas with explicit constraints, not loose `dict` or `Any`.
- [ ] **Extra Fields**: Check if sensitive models forbid extra attributes to prevent unintended mass assignment.
- [ ] **Response Models**: Verify routes declare `response_model` to prevent accidental serialization and exposure of internal database fields (e.g., hashed passwords, internal tokens).

### 2. Security Headers, CORS & Errors
- [ ] **CORS Restrictions**: Verify `allow_origins` does NOT use `["*"]` in conjunction with `allow_credentials=True`.
- [ ] **Error Handling**: Verify global exception handlers do not return Python stack traces, internal paths, or raw database errors in production responses.
- [ ] **Debug Mode**: Confirm FastAPI `debug=False` in production environments.

### 3. Database Lifecycle & Concurrency
- [ ] **Session Leaks**: Confirm database sessions yield within a `try...finally` or context manager to prevent connection pool exhaustion under load.
- [ ] **Blocking Calls in Async Handlers**: Look for synchronous I/O calls (`requests.get`, `time.sleep`, synchronous file I/O, heavy CPU processing) inside async route definitions (`async def`). These block the single-threaded event loop and degrade concurrency. Recommend `httpx.AsyncClient`, `asyncio.sleep`, or offloading to `run_in_threadpool`.

### 4. Background Tasks & Long-Running Jobs
- [ ] **FastAPI `BackgroundTasks` Limitations**: Verify `BackgroundTasks` are only used for trivial, idempotent tasks (e.g., sending an email). Heavy computational tasks or mission-critical workflows must use a dedicated queue (Celery, Arq, RQ, Redis Streams).
- [ ] **Task Failure & Retries**: Ensure failures in background tasks are logged and do not cause silent data loss.

### 5. File Uploads & Resource Protection
- [ ] **Upload Size Limits**: Verify `UploadFile` endpoints enforce file size limits in memory and on disk before processing.
- [ ] **Path Traversal**: Ensure uploaded filenames are sanitized and never written directly to the filesystem using client-provided paths (`os.path.basename` or UUIDs).

### 6. Production Readiness & Observability
- [ ] **Health Check**: Verify `/healthz` or `/health` endpoint checks database connectivity and external dependencies.
- [ ] **Uvicorn / Gunicorn Configuration**: Check deployment commands for worker count, process managers, timeout limits, and signal handling (`SIGTERM`).
- [ ] **Structured Logging**: Confirm Python's `logging` or `loguru` outputs structured JSON logs with request IDs and avoids printing sensitive query parameters.

