---
name: commit-pr
description: Commit pending changes atomically (via the commit skill), then create a pull request — or update the existing PR if one is already open for the current branch. Use when the user asks to "commit and PR", "/commit-pr", "open/update a PR", or wants pending work committed, pushed, and turned into (or synced with) a PR.
---

# Commit + PR

Commit everything atomically, push, then open or update the branch's PR. Invoking this skill authorizes both the commits and the push (committing + pushing publish work — that's the point of the command).

## Workflow
1. **Commit** — run the `commit` skill (analyze the working tree + conversation, make atomic conventional commits). If it branched off the default branch, stay on that branch. If the tree was already clean, continue — there may still be unpushed commits or an existing PR to update.
2. **Push** — no upstream → `git push -u origin <branch>`; otherwise `git push`. If there's nothing ahead of the remote, note it and continue (the PR body may still need refreshing).
3. **Find base + existing PR**:
   - Default branch: `gh repo view --json defaultBranchRef -q .defaultBranchRef.name` (usually `main`).
   - Existing PR: `gh pr view --json number,url,state,title 2>/dev/null` or `gh pr list --head <branch> --json number,url,state`.
4. **Create or update**:
   - **No PR** → `gh pr create --base <default> --head <branch> --title "…" --body-file <tmp>`. Title from the most significant commit; body synthesized from the commits + conversation (what & why, key decisions, testing, out-of-scope). Write the body to a temp file (`mktemp`); never inline a large `--body`.
   - **PR exists** → refresh it with `gh pr edit <n> --body-file <tmp>` so the description reflects the new commits. Never recreate it.
5. **Report** — print the PR URL and whether it was created or updated.

## Notes
- Link the issue (`Closes #NNN`) when the branch name or commits reference one.
- Append the project's required PR-body footer (check CLAUDE.md / an existing PR), e.g. the Claude Code "Generated with" line.
- Match existing PR conventions (title prefix, labels, base branch).
- A denied push/PR is feedback — adjust, don't retry the same command verbatim.
- Don't open a PR from the default branch; the `commit` step should already have branched.
