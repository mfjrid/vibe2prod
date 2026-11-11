# Performance & Reliability Checklist

- [ ] Database indexes match common filters, joins, and sort patterns.
- [ ] N+1 queries and repeated expensive queries are considered.
- [ ] Pagination is used for potentially large datasets.
- [ ] Expensive tasks are moved to queues where appropriate.
- [ ] Queue retries, timeouts, idempotency, and dead-letter/failed-job handling exist.
- [ ] Cache strategy has invalidation and tenant/user isolation.
- [ ] HTTP caching and cache-control are intentional.
- [ ] Static assets are compressed, versioned, and cacheable.
- [ ] Images are resized/compressed; lazy loading used where appropriate.
- [ ] Uploads and exports have size/time/resource limits.
- [ ] Database connection pool/limits and query timeouts are appropriate.
- [ ] External API calls have timeouts, retries with backoff, and circuit-breaker
      behavior where justified.
- [ ] Health checks do not create heavy load.
- [ ] Load/performance tests exist for critical flows when warranted.
- [ ] Resource usage is monitored at application and host/container layers.

Do not run load tests against production without explicit approval and a defined
safe test window.
