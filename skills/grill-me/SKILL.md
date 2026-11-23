---
name: grill-me
description: Interviews the user, one question at a time, to decide the architecture of a product layer (backend, frontend, mobile) and records the choice as an ADR. Use before any code is written on a new product, app, service or major subsystem, when no ADR records the architecture yet, or when the user asks to grill them about an architecture or design decision.
---

# Grill Me

You decide the architecture **with the user**, not for them. No architecture is mandatory: Clean / Onion is one option among many in the catalogs of [ARCHITECTURE.md](../../shared/ARCHITECTURE.md). Your job is to find the product's real forces, narrow the catalog, get an explicit choice, and record it.

## Guardrails

- **Ask one question at a time,** and wait for the answer. For each question, offer 2–4 concrete options and mark your recommendation.
- **Answer from the repository first.** If the code, docs or earlier answers already settle a question, state what you found and skip it.
- **The user's choice wins,** even when it differs from your recommendation. "Keep it simple" is a valid answer.
- **Don't write product code during the interview.**
- **The user owns git history.** Leave the ADR uncommitted.

## Workflow

1. **Check for an existing decision.** Look in `docs/adr/` and the design doc. If an ADR already names the architecture for this layer, report it and stop.
2. **Ask about the forces, in this order,** skipping what you already know:
   - **Domain complexity:** thin CRUD, or real business rules and invariants?
   - **Scale and change:** expected load, how fast the product will change, and its lifetime (prototype or long-lived).
   - **Team:** its size, its experience with the candidate styles, and whether several teams ship independently.
   - **Integrations:** how many external systems (databases, APIs, queues, AI providers), and whether they may be swapped.
   - **Delivery and operations:** deployment shape, hosting, and the team's tolerance for distributed-systems cost.
   - **Layer-specific:**
     - Backend: consistency needs, async workflows, audit trails.
     - Frontend: SEO, client-side business logic, number of teams on the UI, and the framework (React, Next.js, Angular, Vue or Astro).
     - Mobile: offline needs and platform targets.
   - **AI use:** LLM features, RAG, agents, and cost or latency budgets.
3. **Narrow the catalog.** From the matching catalog in ARCHITECTURE.md (backend or frontend), pick the 2–4 styles that fit the answers. For each, give a one-line trade-off tied to this product, and mark your recommendation.
4. **Get an explicit choice.** Ask the user to pick one, or to combine styles (for example, a modular monolith with Hexagonal modules). Confirm any rules the style makes mandatory, such as the dependency rule for Clean / Onion and Hexagonal.
5. **Record the ADR** at `docs/adr/NNNN-architecture.md`, with the context, the options considered, the decision, its consequences, the folder or module layout it implies, and the rules it makes mandatory. Repeat steps 2–5 for each layer the product has.
6. **Hand back.** Tell the caller (the engineer or the tech-lead) the ADR path, so that work can start. Done when every layer has an ADR that the user approved.

## Reference

- **Catalogs and the decision procedure:** [ARCHITECTURE.md](../../shared/ARCHITECTURE.md)
- **System design steps and ADR guidance:** [SYSTEM-DESIGN.md](../../shared/SYSTEM-DESIGN.md)
