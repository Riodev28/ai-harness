# Architecture

This file is shared by every engineering skill: senior-backend-engineer, senior-frontend-engineer, senior-devops-engineer, code-quality-engineer and tech-lead. Read it before you design a new product, a new app, a new service or a major new subsystem. Also read it whenever you review whether code respects the chosen architecture.

## The invariant: clean architecture is mandatory

Every product, on every layer, follows the **dependency rule**. Source-code dependencies point inward, toward the domain:

```
[ UI / delivery ]  →  [ application / use cases ]  →  [ domain ]
[ infrastructure: DB, HTTP, queues, AI providers, browser APIs ]  →  implements ports owned by the inner layers
```

- **The domain holds business rules and types.** It does not import frameworks, databases, HTTP clients, UI libraries or vendor SDKs.
- **Use cases orchestrate the domain** through ports (interfaces) that the inner layers own.
- **Adapters implement those ports at the edge.** Wiring happens at one composition root.
- **The domain and the use cases are testable without any framework.**

Whatever style the user picks below, it sits *on top of* this rule and never replaces it. MVC, for example, becomes the delivery layer of a clean core. It doesn't license putting business logic in controllers.

## The decision: ask the user, per product

The architecture style is the user's decision. Make it once per product, or once per major subsystem:

1. **Check for an existing decision.** Look for `docs/adr/` (an `0001-architecture.md` or similar) or an architecture section in the design doc. If a decision is already recorded, follow it, and don't ask again.
2. **If none exists, ask before writing any code.** Present 2–4 styles from the catalog below that fit this product. Give each option a one-line trade-off, tie it to the product's real forces (domain complexity, scale, team, integrations, AI use), and mark your recommendation. Ask separately for each layer the product has (backend, frontend, mobile).
3. **Wait for the user's answer.** Their choice wins, even when it differs from your recommendation.
4. **Record the decision as an ADR** at `docs/adr/NNNN-architecture.md`. It holds the context, the options considered, the decision, and its consequences. Include the folder or module layout that the decision implies.

## Backend catalog

| Style | Fits when | Watch out |
|---|---|---|
| **Clean / Onion** | The default for any non-trivial service | A ceremony cost on tiny CRUD |
| **Hexagonal (Ports & Adapters)** | Many integrations (DBs, APIs, queues) that may be swapped or tested in isolation | Port proliferation; name the ports after domain needs |
| **DDD (tactical + strategic)** | A rich, evolving business domain with real invariants. Bounded contexts, aggregates, value objects, domain events and a ubiquitous language | Overkill for thin domains; needs domain-expert input |
| **Event-driven** | Decoupled reactions, async workflows, integration between contexts, audit trails. Variants are pub/sub, event sourcing, CQRS and sagas | Eventual consistency, idempotency, ordering, harder debugging. Use an outbox for reliable publishing |
| **Layered / MVC** | Simple CRUD or admin apps, and server-rendered web | Logic leaks into controllers or models. Keep a service/domain layer |
| **Modular monolith** | The default deployment shape. Gives clear module boundaries without network costs | Enforce the boundaries, for example with modules or architecture tests |
| **Microservices** | Independent scaling, team ownership and release cadence are real needs | Distributed-systems cost: network, consistency, ops, observability |
| **Serverless / function-based** | Spiky or low traffic, event-triggered jobs | Cold starts, vendor lock-in, local testing |
| **AI-driven / agentic** | LLM features: an AI provider behind a port, orchestration (pipelines or agent loops with tools), RAG (retrieval → context → generation), structured outputs validated at the boundary, evals as tests, guardrails, cost and latency budgets | Non-determinism. Version the prompts, log inputs and outputs (redacted), and plan fallbacks for provider failure |

## Frontend catalog

| Style | Fits when | Watch out |
|---|---|---|
| **Clean frontend** (domain / application / infrastructure / UI) | Apps with real client-side business logic | Keep it pragmatic; not every component needs layers |
| **Feature-Sliced Design** (`app / pages / widgets / features / entities / shared`) | Medium-to-large SPAs and Next.js apps with many features | Learn and enforce the import rules (lint them) |
| **Feature-based modules + a shared design-system package** | The default for most products | Keep cross-feature imports going through public `index.ts` APIs only |
| **MVVM / presenter + view** | Angular, or complex forms and stateful screens | Presenter bloat |
| **Islands architecture** | Content-heavy sites (Astro) with small interactive parts | Sharing state across islands |
| **Server-first (React Server Components, Next.js App Router)** | SEO, data-heavy pages, minimal client JS | The server/client boundary, caching semantics |
| **Micro-frontends** | Several teams shipping one UI independently | Bundle duplication, design consistency, runtime coupling |
