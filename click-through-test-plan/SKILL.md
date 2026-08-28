---
name: click-through-test-plan
description: Writes a numbered click-through test plan to .context/test-plan-{issue}.md — UI steps and URLs a human follows in a browser, not shell commands and not an automated run. Verifies every credential, port, URL and validation rule against the codebase before writing it down, and solves the setup problem before writing step one. Use when asked for a test plan, test scenarios, QA steps, manual verification steps, "how do I test this", "give me a todos.md", or "steps I can click through" — i.e. whenever the human will run the steps themselves. For the agent to drive the browser instead, use playwright-agent-testing.
---

# Click-through test plan

Produce a document someone follows by hand, in a browser, top to bottom. Numbered so any step
is citable in conversation ("3.2 failed").

## Non-negotiables

**Never invent a fact.** Every credential, port, URL, container name, route, role name and
validation rule gets looked up in the codebase first. A plan with a wrong password or a route
that 404s wastes more time than no plan. Cite `file:line` for each fact in your reply to the
user — not in the document, which stays clean.

**Prefer clicking to typing commands.** No `curl`, `jq`, or `psql` unless the UI genuinely
cannot do the thing. When you do fall back to a command, say in the step why the UI could not.
Starting the stack and running the test suite are the normal exceptions.

**Solve the setup problem before writing step 1.** The usual reason a plan is unusable is that
the state it assumes cannot be reached through the UI. Check the real dialogs and forms first.
For example: if the create-user dialog has no password field, then "give two accounts the same
password" needs some other route — a mail catcher, a self-service reset page, a seed script.
Find that route, or the whole plan collapses at the first fixture step.

**Say which step is load-bearing.** Most steps are scaffolding; usually one or two actually
prove the thing. Mark them and say what a failure there means. A plan where every checkbox
looks equally important hides the assertion that matters.

**Separate "setup broke" from "the feature is broken."** At any step that can fail for a boring
reason, say so inline, at that step. Otherwise a failed pre-condition gets reported as a bug.

## Workflow

1. Read the ticket, the PRD if one exists, and `git diff origin/main...` to see what actually
   changed.
2. Establish the goal in one or two sentences. If the goal and the implementation disagree,
   note it — that becomes section 5, not a checkbox.
3. Look up every environment fact you will need. If the project keeps a reference file of
   environment constants (ports, seeded accounts, container names), read it first — it already
   has the constants, so you skip the lookups. Otherwise read the config, compose file, seeds
   and route definitions directly.
4. Walk the setup path through the real UI. Find the blockers.
5. Write the file from [TEMPLATE.md](TEMPLATE.md) to `.context/test-plan-{issue}.md`. Read any
   existing file at that path first so you never clobber it.
6. Report: where it is, which steps are load-bearing, what you could not verify, and any
   decision you are handing back.

## The eight sections

Always all eight, in this order. Write `_n/a — <one line why>_` under any that does not apply
rather than dropping it; a missing section reads as an oversight.

| # | Section | Holds |
|---|---|---|
| 1 | Setup | Get the environment into the required state. Config that must be set *before* startup goes here, flagged as such. |
| 2 | Fixtures | Build the data. The part most likely to be blocked by UI limits. |
| 3 | The test | The goal itself. Keep it short — if this section is long, scaffolding has leaked in. |
| 4 | Boundary cases | Adjacent behaviour that should still be sane. Include one "did we disturb everyone else" check. |
| 5 | Decisions to make | Where the stated goal and the code genuinely differ. A call to make, with your recommendation — not a pass/fail box. |
| 6 | Build and automated tests | What to compile and run, naming the new tests and what they correspond to in section 3. Note which pre-existing tests must still pass. |
| 7 | Deploy and environment | Per-environment checks. Include "record the before state" when a deploy might change config. |
| 8 | Known-broken on purpose | Descoped or pre-existing defects the tester will hit. **Say plainly not to report these as regressions**, and name the ticket each one lives on. |

Then an appendix: a symptom → cause table for the failures you can predict.

## Details that earn their keep

- Checkbox every step: `- [ ] **3.2** ...`. Section-dot-step numbering, never a flat list.
- Give expected results, not just actions. "→ a Workspace selector appears listing A and B" beats "check the selector".
- Say when a fixture step doubles as evidence — it saves the tester repeating work later.
- State up front what has **not** been run, if you did not run it.
- Tell the tester to write things down when a later step depends on it (a generated password, a list of settings to diff against).
- No time-sensitive claims ("as of today"), and no line numbers in the document itself — they drift. Line numbers belong in your reply.
