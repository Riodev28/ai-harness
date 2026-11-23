# AI harness template

A team of Claude Code skills that works like an engineering team, plus the shared standards they read.

## How the team works

```mermaid
flowchart TD
    U([You: request a product or feature]) --> TL1

    subgraph before [Before any code]
        TL1[tech-lead<br/>functional analysis<br/>docs/analysis/]
        GM{ADR records the<br/>architecture?}
        GR[grill-me<br/>interviews you, picks a style<br/>docs/adr/NNNN-architecture.md]
        SL[tech-lead cuts vertical slices<br/>shared contract first<br/>one brief per slice]
        WT[You create one git worktree<br/>and branch per slice<br/>git worktree add ...]
        TL1 --> GM
        GM -- no --> GR --> GM
        GM -- yes --> SL --> WT
    end

    WT --> ENG

    subgraph build [Build: one slice per worktree, in parallel when independent]
        ENG[senior-backend / frontend / devops engineer<br/>works only in its slice worktree<br/>small steps, test-first<br/>reads QUALITY-PLAYBOOK + RATCHET]
    end

    ENG --> CI

    subgraph ratchet [Ratchet: in CI on the slice branch when the repo has CI]
        CI[CI checks<br/>coverage ≥ 80%<br/>lint set<br/>diff-only format]
    end

    CI -- red --> ENG
    CI -- green --> CQ

    subgraph gate [Quality gate]
        CQ[code-quality-engineer<br/>reviews one slice at a time<br/>reads CI result, runs lint set<br/>refactors without changing behaviour]
        PB[(QUALITY-PLAYBOOK<br/>+ new lint rules)]
        CQ -- every finding becomes a lesson --> PB
    end

    CQ -- CHANGES REQUESTED --> ENG
    CQ -- APPROVED --> TL2

    subgraph signoff [Sign-off]
        TL2[tech-lead<br/>checks slice against analysis, ADR and CI<br/>writes docs/changes/NNNN-slug.md per slice]
    end

    TL2 -- CHANGES REQUESTED --> ENG
    TL2 -- APPROVED --> U2([You: review, commit, merge slices<br/>in dependency order, remove worktree])

    PB -. read before implementing .-> ENG
```

Every change goes engineer → code-quality-engineer → tech-lead → you. You own git history: the skills never commit or push.

## What's inside

- `skills/` holds the roles: `tech-lead`, `senior-backend-engineer`, `senior-frontend-engineer`, `senior-devops-engineer`, `code-quality-engineer`, `grill-me` (picks the architecture with you) and `response-mode` (Concise by default, Explanatory when you ask for detail).
- `shared/` holds the standards every role reads: architecture, principles, design patterns, data structures, system design, the quality playbook, the post-push ratchet rules, and how work is cut into slices and worktrees.

Links between files are relative, so the repository works wherever you clone it.

## Install

You need [Claude Code](https://claude.com/claude-code) and git.

1. Clone the repository anywhere you like:

   ```bash
   git clone https://github.com/Riodev28/ai-harness ~/claude-harness
   ```

2. Link the skills and the shared docs into Claude Code's folder. The skills reach the shared docs through `../../shared/`, so `shared` must sit next to `skills`:

   ```bash
   mkdir -p ~/.claude/skills
   for skill in ~/claude-harness/skills/*/; do
     ln -sfn "$skill" ~/.claude/skills/"$(basename "$skill")"
   done
   ln -sfn ~/claude-harness/shared ~/.claude/shared
   ```

   To use the skills in one project only, link both into that project's `.claude/` instead.

3. Restart Claude Code, then check that the seven skills appear.

## Use

- Ask for a feature and invoke the `tech-lead` skill first. It writes the functional analysis and briefs the engineers.
- With no architecture decision yet, the engineers invoke `grill-me`, which interviews you and records an ADR.
- You can also run a skill directly, for example `/code-quality-engineer`.

## Update

```bash
cd ~/claude-harness && git pull
```

Symlinked skills pick up the changes.

## House rules

- You own git history. The skills may run read-only git, and never commit or push for you.
- After each push, the rules in `shared/RATCHET.md` apply: at least 80% coverage, linter rules written by the code-quality-engineer, and formatting only the diff. If the repository has CI, put them in CI.
