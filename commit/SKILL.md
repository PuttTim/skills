---
name: commit
description: Analyze the working tree plus the conversation context and commit the changes as a series of small, atomic, conventional commits. Use when the user asks to "commit", "commit my changes", "commit atomically", or wants pending work split into logical commits. Commits only — never pushes.
---

# Commit

Group the current changes into the smallest set of self-contained commits and commit each with a clear conventional-commit message. Use the conversation so far to understand *why* each change was made — that intent drives both the grouping and the messages, and is information a bare `git diff` doesn't carry.

## Authorization & safety
- Invoking this skill IS the authorization to commit. Analyze, show the plan, then commit — don't ask for a second yes per commit.
- **Never `push`** here. Pushing is `/commit-pr`.
- If `HEAD` is on the default branch (`main`/`master`), STOP and create a branch first; confirm the branch name with the user before committing.
- If the working tree is clean, say so and stop.

## Workflow
1. **Survey** — `git status --short`, `git diff HEAD`, and `git diff --cached` to see every change; note untracked files.
2. **Learn conventions** — `git log --oneline -15` for message style and any required trailer (e.g. a `Co-Authored-By:` line); skim CLAUDE.md for commit rules. Match what the project already does.
3. **Plan the split** — group changed files into atomic commits (heuristics below). Briefly list the planned commits (message + files) before committing.
4. **Commit each group** — `git add <explicit paths>` then `git commit -m "…"`. Stage paths explicitly per group; never `git add -A` across groups. Use a heredoc for multi-line messages.
5. **Report** — show `git log --oneline` for the new commits and confirm the tree is clean.

## Atomic-grouping heuristics
- **One concern per commit** — a feature, a bug fix, a refactor, docs, and tests are separate commits when separable.
- **Each commit must build and make sense alone** — never split so an intermediate commit is broken (e.g. a frontend reading a field the backend commit hasn't added). Keep tightly-coupled contract changes (the FE + BE of one API shape) together.
- **Isolate the incidental** — pure formatting, unrelated renames, or regenerated noise (binary metadata churn, lockfile-only diffs) get their own commit, or are discarded if meaningless. Call out anything discarded with `git checkout --`.
- **Scale to the change** — a small change is one commit; don't manufacture splits. A large multi-area change is several.

## Message format
- Conventional commits: `type(scope): summary` — `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `build`, `ci`.
- Imperative, lower-case summary. Add a body explaining *why* when it isn't obvious from the diff.
- Append the project's established trailer (from recent log / CLAUDE.md); if none, use the harness-mandated `Co-Authored-By:` line.
