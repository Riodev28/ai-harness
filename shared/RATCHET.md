# Ratchet

These are the checks that every new feature must pass after it is pushed. The bar only moves up: a rule can be tightened, but never loosened to make a change pass.

This file is shared by every engineering skill. The engineers read it before implementing. The code-quality-engineer enforces it, and the tech-lead confirms it before a change reaches the user.

## 1. Test coverage: at least 80% on every deployment

- Every deployment must have **at least 80% code coverage** from tests.
- Coverage is measured in CI. A deployment whose coverage is below 80% does not ship.
- Coverage must not drop from the previous deployment. New code should be covered by tests written in the same change.
- Don't reach the number with tests that assert nothing. The code-quality-engineer rejects them.
- The devops engineer wires the coverage threshold into the pipeline as a failing check.

## 2. Linter rules owned by the code-quality-engineer

The code-quality-engineer should not need to read every diff line by line. Instead, it turns each recurring finding into an automated rule and runs those rules.

- The code-quality-engineer **writes and maintains the linter rules** (lint configuration, custom rules, and scripts) for each project.
- Every lesson added to `QUALITY-PLAYBOOK.md` should become a linter rule when a tool can check it.
- A review means **running the defined lint set** and reading only what the rules cannot cover (design, naming, behaviour).
- Only the code-quality-engineer edits the rule set. Engineers run it before handing off, and fix every violation. They never disable a rule to pass.
- The lint set runs in CI as a failing check.

## 3. Format every change, and only the diff

- Run the formatter on **every new change**, and **only on the code in the diff**. Don't reformat lines the change did not touch.
- Formatting noise must not hide the real change in review.
- **Formatting the whole codebase happens only when the owner asks for it.** When requested, it goes in **one dedicated commit and one dedicated PR**, with no feature or fix work mixed in.
- The user owns git history, so engineers don't create that commit or PR themselves. They prepare it and the owner commits it.

## Enforce in CI first, to save tokens

- If the repository has CI, **put these rules in CI** as failing checks: the coverage threshold, the lint set and a diff-only format check.
- Agents and subagents then **read the CI result** and don't re-run the checks or re-read the code the checks already cover.
- Run the checks locally only when the repository has no CI.
- The devops engineer adds missing checks to the pipeline. The code-quality-engineer keeps the lint rules that CI runs.

## Checklist after pushing a feature

- [ ] Coverage is at least 80% and has not dropped.
- [ ] The code-quality-engineer's lint set passes with no disabled rules.
- [ ] Only the diff was formatted, and there is no unrelated reformatting.
