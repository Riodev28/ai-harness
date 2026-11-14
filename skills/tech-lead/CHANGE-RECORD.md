# Change record

One record per reviewed change, at `docs/changes/NNNN-<short-slug>.md`. `NNNN` is the next number in sequence, zero-padded (`0001`, `0002`, …). The slug is kebab-case and names the behaviour, for example `0007-text-meal-logging`.

## Rules

- **Write for the user reviewing the diff.** Explain what changed and why, in product terms first, then technical terms.
- **Each record describes one change.** When a review loops through several rounds, add each round to the same record's *Review rounds* section rather than creating a new record.
- **Records are append-only history.** Correct a past record by writing a new one that references it.
- **Link, don't copy.** Point to the spec section, ADR or ticket rather than pasting its text.
- **Add one line to `docs/changes/README.md` per record,** newest first:
  `- [NNNN – Title](NNNN-slug.md) — VERDICT · author agent(s) · YYYY-MM-DD`

## Template

```markdown
# NNNN – <Title>

- **Date:** YYYY-MM-DD
- **Author agent(s):** backend-engineer | frontend-engineer | <other>
- **Verdict:** APPROVED | CHANGES REQUESTED | REJECTED
- **Product reference:** <link to spec section / ticket / acceptance criteria>

## Summary
<2–4 sentences: what the user can now do or what changed, and why.>

## Acceptance criteria
| Criterion | Met? | Where |
|---|---|---|
| <criterion from spec> | ✅ / ❌ | `path/to/file.kt:42` |

## Changes
| File | Change |
|---|---|
| `path/to/file` | <what and why, one line> |

## Design notes
<Decisions taken, patterns or data structures chosen and why, alternatives rejected. Link ADRs.>

## Verification
<Exact commands run by the tech lead and their results (build, lint, type check, tests, manual exercise).>

## Quality review (code-quality-engineer)
<The code-quality report(s) for this change, pasted as delivered: verdict, refactors applied, findings, playbook updates.>

## Review rounds
### Round 1 — <verdict>
- **[blocker]** `file:line` — <problem> → <expected fix>
- **[major]** …
- **[minor]** …

### Round 2 — <verdict>
- <resolved / remaining findings>

## Risks and follow-ups
<Known limitations, tech debt accepted, items for later. "None" if none.>

## For the user
<What the user should look at most closely in the diff, plus the suggested git commands for when they approve, e.g. `git add <files>` and `git commit -m "<message>"`.>
```
