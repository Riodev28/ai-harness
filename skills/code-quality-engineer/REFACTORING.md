# Refactoring

Consult this before you change any code under review. A refactoring changes structure and keeps behaviour identical.

## Procedure

1. **Start from green.** The full check suite (build, lint, type check, tests) passes before you touch anything.
2. **Check that tests protect the code.** If the code you're about to restructure has no test covering its behaviour, first add a characterization test that pins down what the code does today. If you can't tell what the correct behaviour is, the finding belongs to the author: stop refactoring that code.
3. **Take one small step at a time.** Apply one named refactoring, run the checks, and confirm they're green before taking the next step. A red check means you undo that step and take a smaller one.
4. **Leave the public surface alone.** Exported APIs, HTTP, event and schema contracts, CLI flags and pipeline triggers stay exactly as they are.
5. **Keep tests honest.** Refactor tests the same way: make them clearer and point their assertions at behaviour. Each test keeps checking what it checked before.
6. **Record every refactoring** in the quality report as the smell, the refactoring applied and the lesson ID.

## Smell → refactoring catalog

| Smell | Refactoring |
|---|---|
| Long function, mixed abstraction levels | Extract Function; Compose Method; Replace Nested Conditional with Guard Clauses |
| Large class or module with several responsibilities | Extract Class or Module; Move Function; Split Phase |
| Duplicated business rule | Extract Function into the owning domain module; Pull Up into the shared abstraction |
| Primitive obsession (raw `String` IDs, `Double` money) | Replace Primitive with Value Object or Branded Type; Introduce Parameter Object |
| Long parameter list, boolean flag argument | Introduce Parameter Object; Split into named functions (Remove Flag Argument) |
| Growing `switch`/`when` on type or status | Replace Conditional with Polymorphism or a Strategy map; State machine for lifecycles |
| Feature envy, message chains | Move Function to the data's owner; Hide Delegate |
| Magic numbers or strings | Replace Magic Literal with a named constant, enum or union literal |
| Mutable shared state | Encapsulate Variable; Replace with immutable value plus pure function |
| Error swallowed or too generic | Replace with a domain error that carries context; move handling to the boundary layer |
| Wrong data structure (a linear scan inside a loop) | Substitute Algorithm: build a map or set index first |
| Framework or I/O leaking into the domain | Extract a port interface and move the call into an adapter. This counts as a refactor only when the public behaviour doesn't change; otherwise the finding belongs to the author |
| Component doing fetch, logic and render (frontend) | Extract a hook, composable or service; split container and presentational parts |
| Hard-coded styles (frontend) | Replace with design tokens and existing primitives |
| Copy-pasted pipeline steps (devops) | Extract a composite action, reusable workflow or shared-library step |
| Unpinned versions (devops) | Pin by digest or SHA. This is behaviour-preserving if the pin resolves to the version currently in use |
| Dead code, commented-out code, debug output | Remove |
| Unclear name | Rename, using the glossary term from the functional analysis |
