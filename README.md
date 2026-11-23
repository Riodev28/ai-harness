# AI harness template

A team of Claude Code skills that works like an engineering team, plus the shared standards they read.

```
engineer (backend / frontend / devops) → code-quality-engineer → tech-lead → you
```

## What's inside

- `skills/` holds the roles: `tech-lead`, `senior-backend-engineer`, `senior-frontend-engineer`, `senior-devops-engineer`, `code-quality-engineer` and `grill-me` (picks the architecture with you).
- `shared/` holds the standards every role reads: architecture, principles, design patterns, data structures, system design, the quality playbook and the post-push ratchet rules.

## Install

You need [Claude Code](https://claude.com/claude-code) and git.

1. Clone the repository anywhere you like:

   ```bash
   git clone <repository-url> ~/claude-harness
   ```

2. Point the skills' links at your clone. The `SKILL.md` files link to the shared docs with absolute paths, so rewrite the old path to yours:

   ```bash
   cd ~/claude-harness
   grep -rl "/home/riodev/Documents/code/claude" skills shared \
     | xargs sed -i "s|/home/riodev/Documents/code/claude|$HOME/claude-harness|g"
   ```

   On macOS, use `sed -i ''` instead of `sed -i`. Skip this step if you cloned to `~/Documents/code/claude`.

3. Link each skill into Claude Code's skills folder:

   ```bash
   mkdir -p ~/.claude/skills
   for skill in ~/claude-harness/skills/*/; do
     ln -sfn "$skill" ~/.claude/skills/"$(basename "$skill")"
   done
   ```

   To use the skills in one project only, link them into that project's `.claude/skills/` instead.

4. Restart Claude Code, then check that the six skills appear.

## Use

- Ask for a feature and invoke the `tech-lead` skill first. It writes the functional analysis and briefs the engineers.
- With no architecture decision yet, the engineers invoke `grill-me`, which interviews you and records an ADR.
- You can also run a skill directly, for example `/code-quality-engineer`.

## Update

```bash
cd ~/claude-harness && git pull
```

Symlinked skills pick up the changes. Run step 2 again if the pull brings new absolute paths.

## House rules

- You own git history. The skills may run read-only git, and never commit or push for you.
- After each push, the rules in `shared/RATCHET.md` apply: at least 80% coverage, linter rules written by the code-quality-engineer, and formatting only the diff. If the repository has CI, put them in CI.
