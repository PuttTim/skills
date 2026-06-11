---
name: playwright-agent-testing
description: End-to-end acceptance testing for a feature branch. Diffs the branch against main, builds a test plan from the changes, runs it in the browser via /playwright-cli (desktop + mobile), verifies database state and network calls, then writes a scored results file. Use when asked to "test my branch", "acceptance test this", "run through the feature", "QA this branch", or "verify my changes in the browser".
---

# Playwright Agent Testing

Automated acceptance testing workflow: diff the branch, build a test plan, execute it in the browser, verify DB + network, write scored results.

## When to use

User has made changes on a feature branch and wants end-to-end verification before PR.

## Prerequisites

- App running locally (URLs documented in `.context/playwright-context.md`)
- `/playwright-cli` skill available
- Login credentials provided by user (or from args)

## Workflow

Execute these phases in order. Each phase produces an artifact the next phase consumes.

### Phase 1 — Scope the changes

1. Run `git diff main...HEAD --stat` and `git log main..HEAD --oneline` to understand what changed.
2. Read the key changed files (endpoints, components, contracts, migrations, entity changes).
3. Check for an existing test plan or PRD in `.context/` — use it if present.
4. If `.context/playwright-context.md` does NOT exist, generate it now (see [Context File](#context-file) below). If it exists, read it — it has the codebase-specific info you need (login flow, DB access, test accounts, navigation structure, etc.).

### Phase 2 — Build the test plan

Write a checklist to `.context/todos.md` (or the filename the user specifies). Structure it like this:

```markdown
# Test Plan: {Feature} (#{issue})

**Prereqs:** {what must be running}

## {Section Name}
- [ ] {Test case — specific, observable outcome}
- [ ] ...
```

**Required sections** (include all that apply to the diff):

| Section | When to include |
|---------|----------------|
| Navigation & Load | Always — verify the page renders |
| Desktop Layout | UI changes |
| Mobile/Tablet Layout | UI changes — resize to 375x812 |
| Interactive Controls | Forms, toggles, dropdowns, etc. |
| Save / Submit — Happy Path | Any write operation |
| Save / Submit — Validation | Any write operation with constraints |
| API Response Shape | New/changed endpoints |
| Role Gates | Endpoint has auth/role checks |
| Database Verification | Any write that persists data |
| Audit Logging | If feature is audited |
| Existing Features Unaffected | Always — spot-check adjacent features |

### Phase 3 — Execute tests

Use `/playwright-cli` to drive the browser. Follow this protocol:

1. **Open browser:** `playwright-cli open {app-url}` (from `.context/playwright-context.md`)
2. **Login:** Use credentials from args or `.context/playwright-context.md`
3. **Navigate to impersonation** if needed (follow the impersonation flow documented in the context file)
4. **For each test case:**
   - Perform the action
   - Take a `snapshot` to verify DOM state
   - Record PASS / FAIL / SKIP with evidence
5. **Mobile testing:** After desktop pass, `playwright-cli resize 375 812`, re-test layout sections, take a screenshot for visual check
6. **Network verification:** Use `playwright-cli requests` to confirm correct HTTP methods, status codes, and endpoints hit
7. **Database verification:** Query the database directly to confirm data was persisted. Use the DB access command from `.context/playwright-context.md`. Watch for case-sensitive column/table names specific to the project's ORM conventions.
8. **Console check:** Use `playwright-cli console` to verify no unexpected errors
9. **Toast capture:** Toasts can disappear fast. Set up a MutationObserver via `playwright-cli run-code` BEFORE triggering the action if you need to capture toast text:
   ```js
   async page => {
     await page.evaluate(() => {
       window.__toasts = [];
       const obs = new MutationObserver(muts => {
         for (const m of muts) for (const n of m.addedNodes)
           if (n.nodeType === 1 && n.getAttribute?.('data-sonner-toast') !== null)
             window.__toasts.push({text: n.textContent, type: n.getAttribute('data-type')});
       });
       obs.observe(document.body, {childList: true, subtree: true});
     });
   }
   ```
   Then after the action: `playwright-cli eval "JSON.stringify(window.__toasts)"`
10. **Role gate testing:** Log in as different role users and verify 403s or UI hiding
11. **Close browser** when done: `playwright-cli close`

### Phase 4 — Write results

Copy the test plan to `{original-name}-results.md` (e.g., `.context/todos-results.md`). Update each item:

- `[x]` — PASS (with brief evidence note if non-obvious)
- `[ ]` — FAIL or SKIP (with `> **FAIL:**` or `> **SKIPPED:**` blockquote explaining what happened)

Add a summary table at the bottom:

```markdown
## Summary

| Section | Pass | Fail | Skip |
|---------|------|------|------|
| ... | ... | ... | ... |
| **Total** | **N** | **N** | **N** |

### Issues Found
1. **{title}** — {description}
```

## Context File

When `.context/playwright-context.md` does NOT exist, generate it using the template in [CONTEXT-TEMPLATE.md](CONTEXT-TEMPLATE.md). This file caches login flow, DB access, tenant IDs, role users, and navigation structure so future runs skip codebase discovery.

**Keep it updated** if you discover new info during testing.

## Tips

- Snapshots are cheaper than screenshots — use snapshots for verification, screenshots only for visual layout checks
- When a ref is stale after a page change, take a fresh `playwright-cli snapshot` before interacting
- For dropdowns/selects: click to open, grep the snapshot for the option, click the option ref
- Auth tokens from network requests can be reused for direct `curl` API testing
- Document any navigation quirks you discover in `.context/playwright-context.md` under "Known Quirks"
