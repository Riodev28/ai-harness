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
