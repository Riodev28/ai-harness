# System design

Consult this when a change crosses a service, data or scaling boundary, or when you're asked to design a system. Work through the steps in order, and write the result as a short design note: an ADR (Architecture Decision Record) for decisions that are hard to reverse.

## 1. Requirements first

- **Functional.** The core use cases, and what is explicitly out of scope.
- **Non-functional.** Expected load (requests per second, reads versus writes), data volume and growth, latency targets (p50/p99), availability target, consistency needs, durability, security and compliance.
- **Back-of-the-envelope estimate.** Work out storage per year, peak requests per second and bandwidth. These numbers decide whether you need caching, sharding or queues. Often you don't.

## 2. Start simple

A well-structured monolith (a modular monolith) on one relational database is the default. Split out a service only when a real force calls for it: independent scaling, a different team, a different release cadence, or fault isolation. Every network hop adds latency, new failure modes and operational cost.

## 3. APIs

- **Shape.** Resource-oriented REST is the default. Use gRPC for internal, low-latency or streaming calls, and GraphQL when clients really need flexible reads.
- **Contract first.** Write the OpenAPI or proto definition, and version it (`/v1`). Changes are additive; breaking changes go into a new version.
- **Correct semantics.** Use the right status codes and one consistent error body (for example RFC 9457 problem details). Make writes idempotent, either by design or with an `Idempotency-Key`.
- **Pagination.** Cursor-based, with a limit on page size.
- **Validation at the edge.** Validate the request DTOs there, and map them to domain types. Never expose persistence entities directly.

## 4. Data

- **Default to relational (PostgreSQL)** for transactional data. Pick another store for a specific access pattern:
  - key-value (Redis) for caches, sessions and rate limits;
  - document stores for aggregates that are naturally document-shaped and need flexible schemas;
  - wide-column stores for massive write throughput;
  - search engines for full-text search;
  - object storage for blobs;
  - time-series databases for metrics.
- **Consistency.** Strong consistency within an aggregate. Between services, use eventual consistency through events. Use sagas with compensating actions for multi-step business transactions.
- **Migrations.** Versioned and forward-only. Make schema changes backward compatible (expand, then migrate, then contract) so deploys need no downtime.
- **Reliable events.** Use the transactional outbox: write the event in the same transaction as the state change, and have a relay publish it. Consumers must be idempotent.

## 5. Scaling

- **Keep app servers stateless,** so they scale horizontally behind a load balancer. Keep sessions in a shared store or in tokens.
- **Caching.** Cache-aside by default. Set a TTL and define invalidation on every write path. Guard against stampedes (request coalescing, jittered TTLs). Cache only when measurements show you need it.
- **Read scaling.** Use read replicas and accept replication lag where reads can tolerate it. Use a CDN for static or public content.
- **Write scaling.** Batching first, then partitioning or sharding on a key that spreads load evenly and keeps related data together.
- **Asynchronous work.** Put slow or bursty work on a queue. Workers are idempotent, failed messages go to a dead-letter queue, and backpressure keeps load in check.
- **Rate limiting.** Use a token bucket per client, enforced at the edge.

## 6. Reliability

- Every remote call has a timeout. Add retries with backoff and jitter on idempotent calls, and circuit breakers on fragile dependencies (see [DESIGN-PATTERNS.md](DESIGN-PATTERNS.md)).
- Degrade gracefully: serve stale data or reduced features instead of failing outright.
- Remove single points of failure that the availability target can't tolerate.
- Provide health checks: liveness and readiness. Shut down gracefully by draining in-flight work.
- Have backups and test restoring them.

## 7. Observability

- **Logs.** Structured JSON, with a correlation or trace ID on every line. Use the right levels, and keep secrets and personal data out.
- **Metrics.** The RED metrics for each endpoint (Rate, Errors, Duration) and the USE metrics for each resource (Utilization, Saturation, Errors).
- **Traces.** Distributed tracing (OpenTelemetry) across service hops.
- **SLOs.** Define SLOs and alert on symptoms users actually feel, not on every cause.

## 8. Security

- Authenticate at the edge (OAuth2/OIDC, short-lived tokens), and authorize every request at the resource level.
- Use TLS everywhere. Keep secrets in a secret manager and never in the repository. Every component gets least privilege.
- Validate and encode input, use parameterized queries, and set request size limits.
- Pin dependencies and scan them for known vulnerabilities.

## 9. Write down the trade-offs

For every significant choice, record what you picked, what you gave up, and what would make you revisit it. Compare at least two options, and recommend one.
