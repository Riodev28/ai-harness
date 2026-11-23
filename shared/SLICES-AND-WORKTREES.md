# Slices and worktrees

This file is shared by every engineering skill. The tech-lead slices the work. The engineers each build one slice in its own git worktree. The code-quality-engineer reviews slice by slice.

## Vertical slices

- **A slice is a thin, vertical cut of behaviour** that a user can observe, for example "log a meal from text". It runs through every layer it needs (data, backend, UI) and can be tested and reviewed on its own.
- **Slice by behaviour, not by layer.** "Build all the screens" is not a slice. "A user can reset their password" is.
- **Keep slices small.** Aim for a diff that one reviewer can read in one sitting. Split a slice that has several independent acceptance criteria.
- **Every slice names its acceptance criteria,** taken from the functional analysis, so "done" is testable.
- **Shared contracts go first.** When slices share an API shape, event or schema, the tech-lead makes the contract its own first slice (or a prerequisite), so that the other slices build against a fixed shape.
- **Order slices by dependency.** Independent slices can run in parallel. Dependent slices wait for the one they depend on to be approved.
- **Each slice leaves the product working.** It passes the ratchet checks in [RATCHET.md](RATCHET.md), and hides unfinished behaviour behind a flag or an unlinked route.

## Worktrees

A **git worktree** is a second working folder on the same repository, on its own branch. Each slice gets its own worktree, so several engineers can work in parallel without touching each other's files.

- **One slice, one worktree, one branch.** Name the branch after the slice (`slice/0007-text-meal-logging`) and put the worktree next to the repository (`../<repo>-worktrees/0007-text-meal-logging`).
- **An engineer works only inside its slice's worktree.** It doesn't edit the main working tree or another slice's worktree.
- **Contracts are the meeting point.** Two slices that touch the same files are a sign the slicing is wrong. Tell the tech-lead, and don't resolve it by editing the other worktree.
- **Changes stay uncommitted** in the worktree for review, as always.

### Who runs the git commands

The user owns git history, and `git worktree add` changes the repository. So:

1. **The tech-lead lists the slices and proposes the exact commands,** for example:

   ```bash
   git worktree add ../<repo>-worktrees/0007-text-meal-logging -b slice/0007-text-meal-logging
   ```

2. **The user runs them,** or explicitly approves the tech-lead or an engineer running them for that slice.
3. **The user does the commits, merges and the cleanup** (`git worktree remove`, branch deletion) when a slice is approved.

Read-only commands (`git worktree list`, `status`, `diff`, `log`) run freely.

## Lifecycle of a slice

1. **Define.** The tech-lead writes the slice into the task breakdown of the analysis, with its acceptance criteria and dependencies.
2. **Brief.** The tech-lead sends a brief that names the slice, its branch and its worktree path.
3. **Create.** The user creates the worktree (see above).
4. **Build.** The engineer builds the slice in the worktree, test-first, and applies the ratchet rules.
5. **Verify.** CI runs on the slice's branch (when the repository has CI). The code-quality-engineer reviews the slice's diff.
6. **Sign off.** The tech-lead checks the slice against the analysis and writes the change record, one record per slice.
7. **Integrate.** The user reviews, commits and merges the slice, in dependency order, then removes the worktree.
