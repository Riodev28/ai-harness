# Principles and clean code

Consult this while you design and implement, and apply all of it when you check a diff against the quality gate.

## Design principles

- **Single Responsibility.** A module has one reason to change. If describing it needs "and", split it.
- **Open/Closed.** Extend behaviour by adding code (a new strategy or handler), not by editing a growing `if`/`when` chain.
- **Liskov Substitution.** A subtype honours its parent's contract: no stronger preconditions, no surprise exceptions.
- **Interface Segregation.** Clients depend on small, focused interfaces, not on wide ones they mostly ignore.
- **Dependency Inversion.** Domain code depends on abstractions it owns (ports), and the infrastructure implements them (adapters). Wire the dependencies at the composition root.
- **Deep modules.** Give each module a small interface that hides real complexity. A wrapper that only forwards calls adds no value.
- **Composition over inheritance.** Inherit only for a true is-a relationship whose contract is stable.
- **KISS / YAGNI.** Build the simplest thing that meets today's requirement. Add an abstraction at the second real use, not in anticipation of one.
- **DRY, about knowledge, not text.** Merge two code paths only when they encode the same rule. Two snippets that merely look alike can stay separate.
- **Law of Demeter.** Talk to direct collaborators. Avoid chains like `a.getB().getC().doX()`.
- **Immutability by default.** Use immutable values and pure functions in the domain, and confine mutation to small, obvious places.
- **Fail fast.** Validate invariants at construction time, so an invalid object cannot exist. Prefer value types like `Email` or `Money` to raw strings and numbers.

## Object Calisthenics, applied pragmatically

Use these nine rules as guidelines, not as hard limits. Bend a rule when following it literally makes the code harder to read, and say why in the review. The first, second, fifth and seventh rules are easy to check with a linter, so the code-quality-engineer should turn them into lint rules (see [RATCHET.md](RATCHET.md)).

1. **Shallow nesting.** Aim for one level of indentation per method, and treat more than two as a smell. Use guard clauses and extract methods.
2. **Avoid `else` where a guard clause is clearer.** A simple two-branch `if`/`else` is fine. Replace long `else` chains with early returns, polymorphism or a lookup table.
3. **Wrap primitives that carry rules.** Make `Email`, `Money`, `OrderId` and `Quantity` value objects. Plain strings and numbers without rules (a label, a page size) can stay primitive.
4. **Give a collection its own type when it has behaviour.** If a list comes with rules (no duplicates, a total, a maximum size), wrap it. A plain list with no rules needs no wrapper.
5. **Respect the Law of Demeter.** Avoid `a.getB().getC().doX()`. Fluent builders, stream pipelines and method chains on the same object are fine.
6. **Don't abbreviate.** Use the full name (see Naming).
7. **Keep units small.** As a guide, aim for functions of about 20 lines or fewer and classes of about 200 lines or fewer. Split when a unit does more than one thing.
8. **Keep few fields, and keep them cohesive.** There is no hard limit of two. When a class grows many fields, check that they belong together, and split it if they don't.
9. **Tell, don't ask.** Put behaviour on the object that owns the data, and avoid code that reads state to decide for the object. DTOs, read models and mapping code are exempt from this rule.

## Domain-Driven Design, tactical building blocks

Use these when the product's ADR picks DDD (see [ARCHITECTURE.md](ARCHITECTURE.md)), or when a part of the domain has real invariants. Thin CRUD does not need them.

- **Ubiquitous language.** Use the domain experts' words for types, methods and tests, and keep them identical across code, docs and conversation. When the word changes, rename the code.
- **Entity.** An object defined by its identity, which stays the same while its attributes change. Compare entities by ID.
- **Value object.** An immutable object defined by its value, with no identity. Compare by value, validate in the constructor, and give it behaviour (`Money.add`, `DateRange.overlaps`).
- **Aggregate.** A cluster of entities and value objects that must stay consistent together, behind one **aggregate root**. Outside code holds a reference to the root only, changes go through the root's methods, and the root enforces the invariants. Keep aggregates small, and reference other aggregates by ID.
- **One transaction, one aggregate.** Change a single aggregate per transaction. Use domain events for consistency between aggregates.
- **Repository.** A collection-like port, owned by the domain, that loads and saves whole aggregates. Define one per aggregate root, with the implementation in the infrastructure layer.
- **Domain service.** Use one for a business rule that doesn't belong to a single entity or value object. It is stateless and uses domain types only.
- **Domain event.** A past-tense fact the domain publishes (`OrderPlaced`). Name events in the ubiquitous language.
- **Factory.** Use one when building a valid aggregate takes more than a constructor can express.
- **Bounded context.** Each context has its own model and its own language. Translate at the boundary with an anti-corruption layer or an explicit contract, and don't share one model across contexts.
- **Keep the domain anemic-free.** Put the rules on entities and value objects, not in application services (see [DESIGN-PATTERNS.md](DESIGN-PATTERNS.md)).

## Naming

- Names reveal intent and domain meaning, such as `overdueInvoices` rather than `list2`.
- Functions and methods are verbs (`calculateTotal`), types are nouns (`InvoiceRepository`), and booleans are predicates (`isActive`, `hasAccess`).
- Use the domain's vocabulary consistently: one word per concept across the codebase.
- Avoid abbreviations, except universal ones like `id`, `url` and `http`.

## Functions

- A function is small, does one thing, and works at one level of abstraction.
- Keep it to three or fewer parameters. Beyond that, introduce a parameter object.
- Avoid boolean flag parameters. Split the function into two named functions instead.
- Separate commands from queries: a function either changes state or returns data, not both.
- Use guard clauses and early returns instead of deep nesting.
- Keep side effects explicit and at the edges, and leave the core pure.

## Errors

- Validate at the system boundary (HTTP, message consumer, CLI) and trust the data inside it.
- Use typed or domain errors (`InvoiceNotFound`), or result types where the codebase uses them, for expected failures. Use exceptions for exceptional ones.
- Add context when rethrowing, and keep the cause.
- Translate errors at layer boundaries. A database exception becomes a domain error, which becomes an HTTP status.
- Let only top-level handlers catch broad exception types: they log the error and map it to a response.

## Comments and docs

- Use comments to explain why: the business rule, a non-obvious constraint, or a link to a ticket or ADR. Let the code say what.
- Document every public API's contract: its inputs, outputs, errors and side effects.

## Testing

- Follow the testing pyramid: many fast unit tests on the domain, fewer integration tests on the adapters (database, HTTP, queue), and a handful of end-to-end tests.
- Structure each test as Arrange, Act, Assert, with one behaviour per test and a name that states it (`rejects_order_when_stock_is_insufficient`).
- Test through public interfaces. Use fakes for ports, and save mocks for verifying interactions that are themselves the behaviour.
- Make tests deterministic: inject clocks, randomness and IDs, and give tests no shared mutable state.
- Run integration tests against real dependencies (for example Testcontainers) instead of mocking the database.

## Code smells to refactor away

Watch for:

- long functions, and large classes known as "god objects";
- primitive obsession;
- feature envy;
- shotgun surgery;
- duplicated rules;
- magic numbers and strings;
- deep nesting;
- temporal coupling;
- hidden global state;
- leaky abstractions.
