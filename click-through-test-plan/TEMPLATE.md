# Template

Skeleton for `.context/test-plan-{issue}.md`. Keep the section order. Replace the bracketed
guidance; do not ship the guidance itself.

---

```md
# How to test {TICKET} / #{ISSUE} — {one-line goal}

Branch `{branch}`. PRD: `.context/prd-{issue}.md`.   ← drop the PRD reference if there is none

**What we are proving:** {the goal in one or two sentences, in plain language. No file paths.}

**What changed:** {one sentence. If the behaviour already worked and only config or tests
changed, say so here — it reframes the whole document.}

{If nothing has been compiled or run, say it here, once, plainly.}

Work through the parts in order. Part 3 is the actual test; everything before it is setup.

---

## Part 1 — Setup

{Config that must be in place BEFORE startup goes first, flagged as such, with the consequence
of skipping it. Then start the stack. Then one cheap step that confirms the setup actually took
effect — do not make the tester discover it failed four steps later.}

- [ ] **1.1** ...
- [ ] **1.2** ...

## Part 2 — Fixtures

{The data the test needs. This is where UI limits bite, so walk the real dialogs first.

At any step that can fail for a boring pre-condition reason, say so inline: "If this is rejected
with X, then Y is not set — go back to 1.1. That rejection is the setup failing, not the thing
we are testing."

When a fixture step doubles as evidence for the goal, say so — it saves repeating work later.}

- [ ] **2.1** ...

{Close with a small table of the end state, so the tester can confirm they are set up correctly
before spending time on Part 3.}

## Part 3 — The test

{The goal itself, and nothing else. Short. Each step is an action and its expected result.

Mark the load-bearing step and say what a failure there means:
"**The important check:** ... If X appears, then {the actual defect}, which is what this ticket
is about."}

- [ ] **3.1** ...
- [ ] **3.2** **The important check:** ...

{One line naming which steps are the goal: "3.2 and 3.6 are the two behaviours the ticket asks
for."}

## Part 4 — Boundary cases

{Adjacent behaviour that should still be sane. Bolded lead-in per item so it is skimmable.
Include one "did we disturb everyone else" check — the ordinary user who is not exercising this
feature. Tell the tester how to undo anything destructive.}

- [ ] **4.1 {Short name.}** ...

## Part 5 — Decisions to make

{Where the stated goal and the implementation genuinely differ. Not pass/fail — a call for the
human, with steps that let them see the difference for themselves, then your recommendation and
what changing it would cost.

If there is genuinely no gap: `_n/a — the implementation matches the stated goal._`}

- [ ] **5.1** ...
- [ ] **5.2** Decide: {the question}. {Recommendation and why. What the alternative costs.}
- [ ] **5.3** {Restore whatever 5.1 changed.}

## Part 6 — Build and automated tests

{What to compile and run. Name new tests and map them back to Part 3 steps. Name the
pre-existing tests that must still pass and why that is the interesting assertion. Call out any
change that automated tests cannot catch — config, workflow, infrastructure files.}

- [ ] **6.1** ...

## Part 7 — Deploy and environment

{Per-environment checks. When a deploy might change config, record the before state first so
there is something to compare against. If a claim about deploy behaviour is unverified, make
checking it a step rather than repeating the claim.

If the change is local-only: `_n/a — nothing environment-specific in this change._`}

- [ ] **7.1** ...

## Part 8 — Known-broken on purpose

{Descoped or pre-existing defects the tester will hit while doing the above. Name the ticket
each one now lives on. State plainly: **do not report these as regressions** — confirming they
behave as they did before is how you know a revert was clean.

If there are none: `_n/a — no known defects in this area._`}

- [ ] **8.1** ({TICKET}) ...

---

## If something goes wrong

| What you see | What it means |
|---|---|
| {predicted symptom} | {cause and the step to go back to} |
```

---

## Reminders

- Section-dot-step numbering throughout, every step a checkbox.
- Expected results, not just actions.
- No `file:line` citations and no version or date claims inside the document — those go in your
  reply to the user, where they can be checked and then discarded.
- Tell the tester to write down anything a later step needs (a generated password, a settings
  list to diff against).
- If a command is genuinely unavoidable, say in the step why the UI could not do it.
