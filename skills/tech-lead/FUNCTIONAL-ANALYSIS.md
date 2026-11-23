# Functional analysis and engineer briefs

The functional analysis is the **product source of truth** that every engineer builds against and that every review checks against. Write one for each product or feature at `docs/analysis/NNNN-<slug>.md`, numbered in sequence. Index each analysis in `docs/analysis/README.md` with one line, newest first:
`- [NNNN – Title](NNNN-slug.md) — STATUS · YYYY-MM-DD`

## Rules

- **Describe what and why, never how.** Implementation choices belong to the engineers and the ADRs. Contracts between engineers are the exception: the analysis fixes them so the engineers can work in parallel.
- **Every acceptance criterion is testable,** written as *Given / When / Then* with concrete values. Avoid vague criteria like "works well" or "fast"; turn them into measurable non-functional requirements.
- **Use the product's vocabulary.** Define every domain term once, in the glossary, and use exactly that term in the briefs, the code and the reviews.
- **Status:** DRAFT, then APPROVED (by the user), then SUPERSEDED (by NNNN). Once the analysis is approved, a scope change becomes a new revision that the user approves, and the briefs it touches are re-sent.
- **Every requirement carries an ID** (`FR-3`, `NFR-2`, `US-4`). Briefs, change records and tests reference these IDs, which keeps them traceable.

## Analysis template

```markdown
# NNNN – <Product / feature name>

- **Status:** DRAFT | APPROVED | SUPERSEDED by NNNN
- **Date:** YYYY-MM-DD · **Approved by:** <user> on YYYY-MM-DD
- **Architecture:** <links to ADRs per layer>

## 1. Context and goals
<The problem, who has it, why now. 2–4 measurable goals.>

## 2. Scope
- **In scope:** …
- **Out of scope:** … (and where it may go later)

## 3. Actors and roles
| Actor | Description | Permissions |
|---|---|---|

## 4. Glossary
| Term | Meaning |
|---|---|

## 5. User stories and acceptance criteria
### US-1 <title>
As a <actor>, I want <capability> so that <benefit>.
- **AC-1.1** Given <state>, when <action>, then <observable result>.
- **AC-1.2** (edge / error case) Given …, when …, then …

## 6. Functional requirements and business rules
| ID | Requirement / rule | Source (US) |
|---|---|---|
| FR-1 | … | US-1 |

## 7. Flows
<Main flow and alternative or error flows per story. Use numbered steps or a Mermaid sequence/state diagram.>

## 8. Data
<Entities, key attributes, relationships, lifecycle states, validation rules, retention.>

## 9. Interfaces and contracts
<API endpoints (method, path, request and response shapes, errors), events (name, payload), external integrations. These are the agreements between engineers.>

## 10. UI and UX
<Screens and navigation, every state (empty, loading, error, success), copy and tone, accessibility, i18n.>

## 11. Non-functional requirements
| ID | Category | Requirement (measurable) |
|---|---|---|
| NFR-1 | Performance | p95 < 300 ms for … |
<Also cover security, privacy, availability, scalability, observability and compatibility.>

## 12. Slices and task breakdown
<Cut the work into vertical slices of observable behaviour (see shared/SLICES-AND-WORKTREES.md). Each slice has one branch and one worktree, and one or more tasks.>

| Slice | Task | Owner skill | Depends on | Covers |
|---|---|---|---|---|
| S-0 Shared contract | T-1 Define API contract + DB schema | senior-backend-engineer | — | FR-1, US-1 |
| S-1 Log a meal from text | T-2 Screens for US-1 | senior-frontend-engineer | S-0 contract | US-1 |
| S-2 Delivery | T-3 Dockerize + CI pipeline | senior-devops-engineer | — | NFR-4 |

## 13. Risks, assumptions, open questions
<Each open question has an owner (usually the user) and blocks a listed task until it is answered.>
```

## Engineer brief template

Send one brief for each task, or for each group of tasks owned by one engineer. Keep it self-contained: the engineer starts cold, without your conversation.

```markdown
# Brief T-N – <title>  (analysis: docs/analysis/NNNN-<slug>.md)

- **Owner skill:** senior-backend-engineer | senior-frontend-engineer | senior-devops-engineer | <other>
- **Slice, branch and worktree:** S-N, `slice/NNNN-<slug>`, `../<repo>-worktrees/NNNN-<slug>` (work only here)
- **Goal:** <one sentence in product terms>
- **Covers:** US-…, FR-…, NFR-…  (read these sections of the analysis)
- **Architecture:** <ADR links>, plus where this code lives in the project

## Acceptance criteria
<The exact AC-x.y from the analysis that this task must satisfy.>

## Contracts
<The API, event or schema shapes this task provides or consumes, copied from §9 so both sides build the same shape.>

## Constraints
<The relevant NFRs, plus anything explicitly out of scope for this task.>

## Depends on / unblocks
<Tasks that must land first, and tasks waiting on this one.>

## Done when
- Every listed acceptance criterion is met and tested.
- The skill's quality gate passes.
- A hand-off has been made to the tech-lead review, with the change left uncommitted.
```
