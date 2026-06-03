---
name: cheatsheet-optimiser
description: Optimize any study cheat sheet (technical or non-technical) through iterative coverage checks, layout compression, readability tuning, speed-strategy preservation, and workflow/tool quick-reference additions. Use this skill whenever the user asks to improve, compress, refactor, expand, compare, or quality-check a cheat sheet for subjects like math, DSA, business analytics, data visualization, statistics, programming, or exams/interviews, even if they do not explicitly say "optimize".
---

# Cheatsheet Optimiser

Produce high-signal, compact cheat sheets without regressions.

## What this skill does

Use this workflow when the user wants to:
- merge sheets,
- reduce whitespace,
- increase topic coverage,
- add step-by-step workflows,
- preserve/restore fast-answer tricks,
- add tool quick workflows (calculator/IDE/spreadsheet/SQL/notebook),
- compare against syllabus/question bank/slides/reference docs.

Prioritize: **coverage -> speed -> readability -> density**.

## Inputs to collect

Gather these first (infer from repo if possible):
1. Primary cheatsheet file(s) and target output file name.
2. Source of truth for coverage (question bank, syllabus, lecture notes, PDF).
3. Required visual style constraints (e.g., "follow cheatsheet2 style").
4. Allowed compression level (font, margin, columns).
5. Tool context for quick commands (e.g., TI-84, Excel, Python, SQL, Tableau, IDE).

If anything is missing, choose safe defaults and proceed.

## Non-regression rule

Before editing, identify high-value content that must not disappear:
- foundational concepts and definitions,
- high-frequency methods/frameworks,
- common traps and edge cases,
- high-yield elimination heuristics,
- quick command/menu workflows for relevant tools.

If replacing structure, re-check these are still present after rewrite.

## Optimization workflow

Follow this sequence:

1. **Coverage Map**
   - Parse target references (question bank/syllabus/PDF notes).
   - Build a topic checklist grouped by domain modules (concepts, methods, applications, tools).

2. **Gap Audit**
   - Mark each topic as: `covered`, `partial`, or `missing`.
   - Record specific missing formulas/workflows (not vague statements).

3. **Structural Refactor**
   - Preserve established styling macros and color system.
   - Reorder content by exam frequency and solve speed.
   - Keep formulas near methods they support.

4. **Density Pass**
   - Reduce margins and gutters conservatively.
   - Use breakable sections where large vertical gaps appear.
   - Compress repeated wording; keep key expressions/examples explicit.
   - Do not over-compress to the point of unreadability.

5. **Speed Layer Pass**
   - Add/refresh an explicit "Speed Tricks" block.
   - Include elimination checks relevant to the domain (units, signs, shape/trend, constraints, sanity checks, option testing).

6. **Tools Pass**
   - Add practical quick steps only where tools materially improve speed/verification.
   - Include command/menu patterns for active tools (e.g., calculator commands, spreadsheet formulas, SQL snippets, Python one-liners, plotting shortcuts).

7. **Compile & Verify**
   - Compile Typst output.
   - Fix syntax issues and invalid math tokens.
   - Confirm no key section regressions.

8. **Comparison Notes**
   - If reference material exists, write a concise comparison note file:
     - what is now covered,
     - what remains partial/missing,
     - what to add next iteration.

## File-format quality checks

When editing formatting-heavy sheets:
- Preserve existing macros/design system unless user requests redesign.
- Keep section blocks breakable if pagination leaves large empty regions.
- Ensure symbols/expressions render validly in the target format.
- Keep notation and terminology consistent across sections.

## Required content blocks (generic exam/interview version)

Ensure these blocks exist unless user opts out:
1. Core concepts/definitions.
2. Method-selection workflow (how to choose approach).
3. High-frequency templates/patterns.
4. Edge cases/common mistakes.
5. Speed tricks / option-elimination checklist.
6. Tool quick-reference strip.

For technical domains, add domain modules as needed:
- algorithms/data structures,
- statistics/probability,
- business analytics metrics,
- data visualization principles,
- SQL/data pipelines,
- optimization/model evaluation.

## Output format

After edits, report in this structure:

1. What changed (by file).
2. Coverage added (bullet list of concrete topics/patterns).
3. Density/layout changes (margins/font/gutters/breakability).
4. Speed tricks retained or improved.
5. Compile result and warnings.
6. Optional next step (only if useful).

## Style rules for generated cheatsheets

- Keep formulas concise and exact.
- Put "how to choose method" near formula banks.
- Prefer short imperative solve steps for beginners.
- Preserve user’s existing visual language if a prior sheet exists.
- Avoid deleting useful content unless replaced by better equivalent.

For non-math topics, translate this to:
- compact rules/heuristics,
- decision trees,
- canonical examples,
- anti-patterns,
- quick command references.

## Example triggers

- "compress this cheat sheet, too much empty space"
- "compare with this question bank and fill missing topics"
- "keep formatting of file B, merge with file A"
- "add quick commands for Excel/SQL/Python"
- "make it faster for MCQ/interview elimination"
- "optimize this DSA revision sheet"
- "refactor this biz analytics cheat sheet"
- "improve this data-vis study summary"

## Example response actions

- Create `refactor*.md` plans for gap analysis before large rewrites.
- Produce iterative versions (`cheatsheet6b`, `6c`, `6d`) with explicit change goals.
- Run compile checks each iteration and patch syntax issues immediately.

## Domain overlays (apply selectively)

### DSA overlay
- Add complexity table (`time/space`) for core operations.
- Add pattern map (two pointers, sliding window, binary search, DFS/BFS, DP, greedy, union-find, heaps).
- Add debugging checklist (off-by-one, visited state, boundary cases, duplicates, overflow).

### Business analytics overlay
- Add KPI glossary (retention, churn, CAC, LTV, conversion, margin).
- Add interpretation templates (what increase/decrease implies).
- Add metric sanity checks (denominator, period alignment, cohort consistency).

### Data visualization overlay
- Add chart-choice guide by analytical goal.
- Add encoding hierarchy (position > length > angle > color hue).
- Add common misread traps (truncated axes, dual-axis misuse, clutter, color misuse).

### Quant/stat overlay
- Add distribution/test-selection decision tree.
- Add assumptions checklist and interpretation caveats.
- Add quick formulas and when-not-to-use notes.

## Typst starter templates (2/3/4 columns)

Use these snippets when creating or refactoring `.typ` cheat sheets. Keep style tokens centralized (`colors`, `section-box`, `subheader`) so layout changes are low-risk.

### Variation A: 2-column (readability-first)

```typst
#set page(paper: "a4", margin: 0.45cm, flipped: true)
#set text(font: "Linux Libertine", size: 7pt)

#let section-box(title, color, body) = {
  block(
    width: 100%,
    breakable: true,
    stroke: 0.5pt + color,
    radius: 4pt,
    [
      #block(width: 100%, fill: color, inset: 3pt, text(fill: white, weight: "bold", title))
      #block(width: 100%, inset: 4pt, body)
    ]
  )
}

#columns(2, gutter: 0.5em)[
  #section-box("Core Concepts", rgb("#8e44ad"))[ ... ]
  #v(0.2em)
  #section-box("Methods", rgb("#3498db"))[ ... ]
]
```

### Variation B: 3-column (balanced density)

```typst
#set page(paper: "a4", margin: 0.35cm, flipped: true)
#set text(font: "Linux Libertine", size: 6.6pt)

#let section-box(title, color, body) = {
  block(width: 100%, breakable: true, stroke: 0.45pt + color, radius: 3pt, [
    #block(width: 100%, fill: color, inset: 2.6pt, text(fill: white, weight: "bold", size: 7pt, title))
    #block(width: 100%, inset: 3pt, body)
  ])
}

#columns(3, gutter: 0.38em)[
  #section-box("Concepts", rgb("#9b59b6"))[ ... ]
  #v(0.1em)
  #section-box("Workflows", rgb("#e67e22"))[ ... ]
  #v(0.1em)
  #section-box("Speed Tricks", rgb("#e74c3c"))[ ... ]
]
```

### Variation C: 4-column (max density)

```typst
#set page(paper: "a4", margin: 0.25cm, flipped: true)
#set text(font: "Linux Libertine", size: 6.1pt)

#let section-box(title, color, body) = {
  block(width: 100%, breakable: true, stroke: 0.42pt + color, radius: 3pt, [
    #block(width: 100%, fill: color, inset: 2.3pt, text(fill: white, weight: "bold", size: 6.9pt, title))
    #block(width: 100%, inset: 2.6pt, body)
  ])
}

#columns(4, gutter: 0.30em)[
  #section-box("Core", rgb("#9b59b6"))[ ... ]
  #v(0.08em)
  #section-box("Methods", rgb("#8e44ad"))[ ... ]
  #v(0.08em)
  #section-box("Applications", rgb("#3498db"))[ ... ]
  #v(0.08em)
  #section-box("Quick Checks", rgb("#e67e22"))[ ... ]
]
```

### Choosing the variation

- Use **2-column** when users want readability and annotation space.
- Use **3-column** as default for mixed depth and speed.
- Use **4-column** when users explicitly want maximum info density.

When changing column count, preserve section order and high-yield blocks first to avoid content regressions.
