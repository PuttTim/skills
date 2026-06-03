---
name: diff-main-summary
description: Generate a markdown PR-review summary that diffs the current git branch against main (or a specified base) and writes it to a file under /tmp named after the branch. Use this whenever the user invokes `/diff-main-summary`, asks for a "review summary" or "PR brief" against main, wants a structured digest of what their branch changed before opening a PR, or needs a reviewer-facing document covering scope, key decisions, what ships, and follow-ups. Also triggers on phrases like "summarize my branch", "diff me against main", or "prep this branch for review".
---

# diff-main-summary

Produces a markdown review brief at `/tmp/<branch>-summary.md` that compares
the current branch to a base (default `main`) and presents it the way a PR
reviewer wants to read it: scope first, then the *decisions* and *why*, then
what actually ships, then follow-ups.

This is a working document for a review session, not a deliverable artifact.
Do not stage or commit it.

## Invocation

- `/diff-main-summary` — base defaults to `main`.
- `/diff-main-summary <base-branch>` — diff against `<base-branch>` instead.
- `/diff-main-summary --no-verify` — skip the build/typecheck step.

The `--no-verify` flag can be combined with a base: `/diff-main-summary develop --no-verify`.

## What this skill is for

The git diff alone tells you *what* changed. The valuable signal for a reviewer
is *why* — and that lives in commit message bodies and in inline code comments
(especially `// Why:` / `// Reason:` style), not in the diff. The reviewer also
wants a navigation aid: which files are big, which are new, which are riskiest,
what was considered and rejected.

This skill recovers that signal and assembles it into a structured document.

## Procedure

Execute these steps in order. Use `git` for all version-control queries, `Read`
for files whose purpose needs to be inferred (the new-files table), `Bash` for
verification commands, and `Write` for the final summary.

### 1. Resolve branch and base

```bash
git rev-parse --abbrev-ref HEAD            # current branch
```

Base branch comes from the slash command argument; default to `main`. If the
caller passed `--no-verify`, remember it; that flag is order-independent.

If `<base>` is not a valid ref (`git rev-parse --verify <base>` fails), stop
and ask the user which base they meant. Don't silently fall back.

### 2. Compute the output path

Replace `/` with `-` in the branch name, then write to `/tmp/<safe-branch>-summary.md`.

**Examples:**
- `user/putt/refactor/todo-list-page` → `/tmp/user-putt-refactor-todo-list-page-summary.md`
- `feat/auth` → `/tmp/feat-auth-summary.md`
- `main` → `/tmp/main-summary.md`

### 3. Gather scope

Run these in order:

```bash
git log <base>..HEAD --oneline                                # commit list
git diff <base>..HEAD --stat                                  # files + line counts
git log <base>..HEAD --pretty=format:"%h %s%n%n%b%n---"       # commit bodies (rationale)
git diff <base>..HEAD --diff-filter=A --name-only             # new files
git diff <base>..HEAD --stat | sort -k3 -n -r | head -10      # top 10 biggest changes
```

The commit bodies matter most — that's where decisions and rationale live. Read
them carefully before drafting Key decisions.

### 4. Recover decisions and rationale

Decisions and "why" can be reconstructed from three sources, in order of
priority:

1. **Commit message bodies.** The author's explanation of the change.
2. **Inline code comments**, especially `// Why:`, `// Reason:`, `// Removed:`,
   `// TODO:`, `// NOTE:`. Grep the changed files for these:
   ```bash
   git diff <base>..HEAD --diff-filter=AM --name-only | xargs grep -nE '(Why:|Reason:|TODO:|Removed:|NOTE:)' 2>/dev/null
   ```
3. **The diff itself as signal.** New helpers, extracted components, renamed
   symbols, deleted abstractions — these indicate abstraction choices even when
   nobody wrote them down.

Use `Read` to open specific files when you need more context than the diff
gives you (especially for new files where the docstring or exports reveal the
file's purpose).

### 5. Run verification (skip if `--no-verify`)

Pick commands based on what's in the repo:

- **JS/TS workspace** (a `package.json` exists somewhere with `tsc` available):
  ```bash
  cd <frontend-dir> && npx tsc --noEmit
  ```
- **.NET solution** (`.sln` file present):
  ```bash
  dotnet build <api-project> --nologo
  ```
- **Other stacks**: use the most idiomatic typecheck/build for whatever is
  there (e.g. `cargo check`, `go build ./...`, `mypy .`). If you can't tell,
  skip rather than guess wrong.

Capture **exit codes only** — don't paste full build output into the summary,
it bloats the document. If a command fails, note that it failed and give the
single most informative line (e.g. the first compiler error).

### 6. Check push and working-tree status

```bash
git status --short                            # working tree state
git rev-parse --verify origin/<branch> 2>/dev/null && \
  git log origin/<branch>..HEAD --oneline     # unpushed commits, if any
```

Note whether the branch exists on origin at all, and whether the working tree
is clean. The summary file itself doesn't count as dirt (it lives in `/tmp`,
not the worktree) — but if you do see real uncommitted changes, mention them.

### 7. Write the summary

Use `Write` to produce the file at the path computed in step 2. Follow the
structure below exactly.

### 8. Report back

Print the absolute output path in chat so the user can open it directly. Do
not paste the summary contents into chat — the file is the deliverable.

## Output structure

Use these headings, in this order. Markdown only. Section headings `##`,
subsections `###`.

### Title and metadata

```markdown
# <Branch label> — review summary

**Branch:** `<branch>` → `<base>`
**Scope:** N commits, M files, +X / -Y lines
```

The branch label is the branch name with a short human description if obvious
from the prefix (e.g. `user/putt/refactor/todo-list-page` → "Todo list refactor").
If unclear, just use the branch name.

### Commits

A markdown table, **oldest → newest**. Two columns: short SHA (7 chars) and
Subject.

```markdown
## Commits

| SHA | Subject |
| --- | --- |
| abc1234 | First commit subject |
| def5678 | Second commit subject |
```

### Key decisions

6–12 bullets. Each bullet leads with the **decision in bold**, followed by a
brief WHY: the constraint that drove it, the alternative considered, the
rationale.

Cover three flavors of decision:
- **Architectural** — endpoints, schemas, shared components, module boundaries.
- **Policy** — permissions, feature gates, allowlists, defaults.
- **UX** — layout, button placement, copy, color/iconography.

Pull these from commit message bodies primarily, code comments secondarily,
diff structure (new helpers, extracted components) as a fallback signal.

If a decision's rationale isn't recoverable from the available signals, say
so plainly. **Do not fabricate a rationale.** Write something like:

> **Switched the upload endpoint to streaming.** Rationale not captured in
> the commit body; flag for author.

Honesty here matters more than completeness — a reviewer who sees an invented
"why" will lose trust in the whole document.

#### Considered alternatives, rejected or parked

Subsection under Key decisions. 3–8 bullets of work that was discussed and did
not land, and why. Sources:
- Commit bodies that mention reverted or considered work.
- Parked TODOs in the code.
- `// Removed:` style comments.
- "Decided against X because Y" phrases in commit bodies.

If there's genuinely nothing here, omit the subsection rather than padding it.

### What ships

One subsection per commit, in the order the commits land. Group multiple small
commits if they collectively land one feature (note the grouping in the
heading: `### feat(auth): JWT support — commits a1b2c3d, e4f5g6h`).

For each unit:

- A **brief structural summary** at the architectural level. Not a line-by-line
  diff — say what new capability or change exists and where it lives.
- **Sub-bullets by area** when areas overlap: Backend / Frontend / Infra /
  Tests. Skip areas that don't apply.

If the diff is huge (>50 files or >5,000 lines), split this section into more
granular subsections so reviewers can navigate. Headings like
`### Backend — auth refactor`, `### Frontend — login screen rebuild` are fine.

### New files

Markdown table. Two columns: Path | Purpose. Purpose is one short line,
derived from the file's docstring, its top-level exports, or the commit
message that introduced it. Use `Read` on the file when needed.

```markdown
## New files

| Path | Purpose |
| --- | --- |
| `src/auth/jwt.ts` | JWT signing and verification helpers used by the auth middleware. |
```

### Things to look at during review

3–6 bullets pointing the reviewer at the most consequential files. For each,
say **what to verify**, not just "look at this file":

> - `src/todos/completed-status-guard.ts` — confirm `allowOnCompleted` is only
>   `true` when both `isCompleted` returns true *and* the helper explicitly
>   allowed it. Easy to invert.

Source candidates: the top-10-biggest-changes list from step 3, files
containing renamed exports, files with new permissions or gating logic, files
with non-obvious state transitions.

### Known follow-ups (not in this PR)

3–6 bullets of TODOs, parked items, and scope-creep that was deliberately
deferred. Sources:
- Inline `TODO` / `FIXME` comments added in this branch (find them with the
  grep from step 4).
- Mentions of "parked", "follow-up", "next PR" in commit bodies.
- Decisions deferred in the rejected-alternatives subsection that still need
  to ship eventually.

### Verification status

Bullet list of verification commands run and their exit codes.

```markdown
## Verification status

- `npx tsc --noEmit` (frontend/) — exit 0
- `dotnet build src/Api/Api.csproj --nologo` — exit 0
- Tests — skipped (no test command configured)
```

When the user passed `--no-verify`, list each command you *would* have run
and mark it `skipped`:

```markdown
- `npx tsc --noEmit` — skipped (--no-verify)
```

### Push status

Two bullets:

```markdown
## Push status

- `origin/<branch>` exists: yes / no
- Working tree: clean / dirty (note any uncommitted changes besides the
  summary file itself, which lives in /tmp)
```

If the branch is pushed but ahead of origin, say so and list the unpushed
SHAs.

## Style and constraints

- **Markdown only.** Tables for tabular data.
- **Short bullets.** Lead with the fact or decision, follow with brief why.
- **Do not reproduce the diff line-by-line.** Summarize at the architectural
  level. A reviewer can run `git diff` themselves; what they can't easily
  do is reconstruct the *why*.
- **Be honest about gaps.** If rationale isn't recoverable, say so. Fabricated
  rationale is worse than no rationale.
- **Skip secrets.** Never surface env values, API keys, tokens, passwords, or
  credentials even if they appear in the diff. If you see what looks like a
  secret, flag it in "Things to look at during review" as a security concern
  without quoting the value.
- **Don't quote diffs verbatim** beyond a few lines. The point is to summarize.
- **Filename uses slash-to-dash replacement.** `user/putt/refactor/todo-list-page`
  → `/tmp/user-putt-refactor-todo-list-page-summary.md`. No other transformation.
- **Do not commit the summary.** It's a working document, not a deliverable.

## Edge cases

- **No commits between base and HEAD.** Tell the user "Branch has no commits
  ahead of `<base>` — nothing to summarize." Don't write an empty file.
- **Detached HEAD.** Use the short SHA as the branch label and produce
  `/tmp/<sha>-summary.md`. Note the detached state in the metadata block.
- **Base ref doesn't exist.** Stop and ask which base the user meant.
- **Huge diff (>50 files or >5,000 lines).** Use the more granular What ships
  subsections, and consider grouping commits.
- **Merge commits in the range.** Use `--first-parent` on `git log` if the
  history is noisy with merge commits from `<base>`:
  ```bash
  git log <base>..HEAD --oneline --first-parent
  ```
- **Binary files in the diff.** `--stat` will show them but with no line
  counts. List them in the appropriate "What ships" subsection without trying
  to summarize their contents.

## Worked example (abbreviated)

For a branch `feat/completed-todos` with 4 commits touching 12 files, the
output file at `/tmp/feat-completed-todos-summary.md` should look roughly like:

```markdown
# Completed to-do items support — review summary

**Branch:** `feat/completed-todos` → `main`
**Scope:** 4 commits, 12 files, +387 / -94 lines

## Commits

| SHA | Subject |
| --- | --- |
| a1b2c3d | Add isCompleted helper |
| e4f5g6h | Wire completed-status guard into to-do mutations |
| 7h8i9j0 | Frontend: disable edit buttons on completed to-dos |
| k1l2m3n | Tests for completed-status guard |

## Key decisions

- **Completed-status check lives in a single helper (`isCompleted`) rather than
  inline checks per mutation.** Per commit body on a1b2c3d: previous attempt
  inlined the check and a follow-up bug missed one mutation site.
- **`allowOnCompleted` defaults to `false`.** Forces callers to opt in
  explicitly, which the commit body on e4f5g6h frames as the safer default
  after the audit found three implicit-allow sites in main.
- ...

### Considered alternatives, rejected or parked

- **A `CompletedItemGuard` middleware was prototyped and dropped** (mentioned
  in e4f5g6h body) — too coarse, couldn't express per-field allowances.

## What ships

### feat: completed-status helper and guard — commits a1b2c3d, e4f5g6h

Introduces a single source of truth for "is this to-do completed" and wires
it into all to-do mutations.

- Backend: new `src/todos/is-completed.ts` helper; mutation handlers updated
  to call `assertEditable(item, { allowOnCompleted })`.
- Tests: helper has unit coverage; mutation handler tests updated for the
  new guard path.

...

## New files

| Path | Purpose |
| --- | --- |
| `src/todos/is-completed.ts` | Helper that returns whether a to-do is in a completed status; single source of truth used by mutation guards. |
| `src/todos/__tests__/is-completed.test.ts` | Unit tests for the helper. |

## Things to look at during review

- `src/todos/mutations.ts` (lines 88–142) — confirm `allowOnCompleted` is
  only `true` for the explicit reopen mutation. Easy to invert.
- ...

## Known follow-ups (not in this PR)

- Admin override path (`TODO` in `mutations.ts:147`) — deferred to follow-up.
- ...

## Verification status

- `npx tsc --noEmit` (frontend/) — exit 0
- `dotnet build src/Api/Api.csproj --nologo` — exit 0

## Push status

- `origin/feat/completed-todos` exists: yes, in sync with HEAD
- Working tree: clean
```

Then in chat:

> Wrote summary to `/tmp/feat-completed-todos-summary.md`.
