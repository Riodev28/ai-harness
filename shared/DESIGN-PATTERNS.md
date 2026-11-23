# Design patterns

Consult this when you face a design decision. A pattern earns its place when it removes a concrete problem that exists today, and the design note should name that problem. When a plain function or a data class solves it, use that.

## Architecture-level

- **Hexagonal / Ports and Adapters (Clean Architecture).** The domain and its use cases sit at the center and own their interfaces (ports). The database, HTTP, queues and external APIs are adapters at the edge. Dependencies point inward. A good fit for a non-trivial service, when the ADR chose it.
- **Layered (controller → service → repository).** Fine for simple CRUD. Keep business rules in the service or domain layer, never in controllers or SQL.
- **Repository.** A collection-like interface over persistence, defined in domain terms (`findOverdue(asOf)`) rather than generic SQL plumbing.
- **Unit of Work / transaction boundary.** One business operation runs in one transaction, and the use case owns that transaction.
- **CQRS.** Split read models from write models when their shapes or scaling needs really differ. Leave it out of simple CRUD.
- **Event-driven / domain events.** Publish facts (`OrderPlaced`) so other modules can react without coupling to each other. Pair this with the transactional outbox (see [SYSTEM-DESIGN.md](SYSTEM-DESIGN.md)) to make publishing reliable.

## Creational

- **Factory / factory method.** Centralizes construction that involves validation or the choice of an implementation.
- **Builder.** For objects with many optional parameters. In Kotlin or Python, prefer named and default arguments.
- **Dependency injection.** Pass dependencies in through constructors and wire them at the composition root. A service locator or a static singleton hides dependencies, so use DI instead.

## Structural

- **Adapter.** Wraps a third-party API behind your own port, so vendor types never leak into the domain.
- **Decorator.** Adds cross-cutting behaviour (caching, retries, metrics, logging) around an interface without editing the implementation.
- **Facade.** One simple entry point over a messy subsystem.
- **Proxy.** Lazy loading, access control or remote access that keeps the same interface.

## Behavioural

- **Strategy.** Interchangeable algorithms selected at runtime, such as pricing or ranking rules. It replaces a growing `switch`.
- **Template method.** A fixed sequence of steps with variable parts. Prefer Strategy, which works by composition.
- **Chain of responsibility / middleware.** Request pipelines for auth, validation, rate limiting and so on.
- **Observer / pub-sub.** Notify many listeners. Out of process, this becomes a message broker.
- **Command.** An operation as an object, which you can queue, retry, log or undo.
- **State machine.** An entity with explicit lifecycle states (an order going `PENDING → PAID → SHIPPED`). Make the transitions explicit and validated; scattered status flags drift out of sync.
- **Specification.** Composable business rules or filters (`isOverdue and not disputed`).

## Resilience patterns

- **Retry with exponential backoff and jitter.** Use it only for idempotent operations and transient errors, and cap the number of attempts.
- **Circuit breaker.** Stop calling a failing dependency, and fail fast or degrade until it recovers.
- **Timeout.** Every network call has one.
- **Bulkhead.** Isolated pools, so one slow dependency can't use up every thread.
- **Idempotency key.** Lets a client retry a write safely, and lets the server deduplicate it.

## Anti-patterns to steer away from

- **God object.** Split it by responsibility.
- **Anemic domain model with rules scattered across services.** Move behaviour onto the entities and value objects.
- **Singleton holding mutable global state.** Use injected instances instead.
- **Speculative generality.** Go back to the concrete code until a second real use case shows up.
- **Distributed monolith,** meaning microservices that have to be deployed together. Merge them, or decouple them properly.
