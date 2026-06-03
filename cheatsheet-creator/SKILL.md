---
name: cheatsheet-creator
description: >
  Create high-density, exam-ready cheatsheets from course lecture notes, PDFs, slides,
  and textbooks. Outputs a multi-page Typst (.typ) document compiled to PDF with
  color-coded sections, formula tables, visual diagrams, exam-ready sentence templates,
  and a printable index. Use this skill whenever the user asks for a cheatsheet, study
  guide, revision sheet, exam prep summary, course notes condensed, or "PYP" / practice
  paper from lecture materials — even if they don't explicitly mention Typst or PDF.
  Also trigger when the user wants to compress, merge, or optimise existing cheat sheets,
  or add formulas / diagrams / answer templates to a study summary.
compatibility: Requires Typst compiler (`typst compile`). Python 3 with venv for PDF extraction.
---

# Cheatsheet Creator

## What this skill does

Transforms scattered course materials (PDF slides, lecture notes, exported slide decks)
into a single, printable, high-signal cheatsheet. The workflow mirrors what a student
actually needs before an exam: coverage mapping, gap filling, formula banks, visual
diagrams, and copy-paste answer templates.

**Output:** A compiled multi-page PDF (from Typst) with:
- 3-column dense layout (or 2/4 columns if user prefers)
- Color-coded section boxes per chapter/topic
- Formula tables and calculation shortcuts
- ASCII / table diagrams (funnels, pyramids, flows, dashboards)
- Exam-ready sentence templates (midterm/final style)
- Printable index box for jumping between sections

## Workflow

Follow this sequence. Do not skip steps — each builds on the previous.

### 1. Discover Inputs

Identify the source material:
- Folder of PDF lecture slides / notes
- Syllabus / question bank (for coverage mapping)
- Existing cheatsheets the user wants merged or improved
- Target output file name (default: `cheatsheet-lec.typ`)

If PDFs are present, use the bundled script to extract text:
```bash
cd <project-dir>
python3 -m venv venv
source venv/bin/activate
pip install pdfplumber
python3 scripts/extract_pdfs.py
```

Read all extracted `.txt` files to build the content corpus.

### 2. Coverage Map & Gap Audit

- Group topics by course modules (e.g., Foundations, Channels, Measurement).
- Mark each topic: `covered`, `partial`, or `missing`.
- Record specific missing formulas / workflows, not vague statements.

### 3. Structural Refactor

- Preserve established macros and color system.
- Reorder content by exam frequency and solve speed.
- Keep formulas near the methods they support.

### 4. Density Pass

- Reduce margins and gutters conservatively.
- Use breakable sections where large vertical gaps appear.
- Compress repeated wording; keep key expressions explicit.
- Do not over-compress to the point of unreadability.

### 5. Speed Layer Pass

- Add an explicit "Speed Tricks" block with:
  - Channel-goal matching shortcuts
  - Quick diagnostic trees (low traffic → high CAC)
  - Elimination checklists for MCQ scenarios

### 6. Tools Pass

- Add practical quick steps only where tools improve speed:
  - GA4 report navigation
  - Google Ads hierarchy shortcuts
  - Calculator formulas (CAC, LTV, ROAS, margins)

### 7. Visual Diagrams Pass

For every major concept, create a visual aid using Typst `table`, `grid`, or `box`:
- **Funnels:** Awareness → Engagement → Conversion (stacked boxes)
- **Pyramids:** Minto Pyramid (L1 / L2 / L3 stacked blocks)
- **Flows:** RTB ecosystem, Hero's Journey 3-act table
- **Dashboards:** KPI trend tables, attribution model credit splits
- **Decision Trees:** Crisis response flow, channel selection logic

See `references/typst-templates.md` for copy-paste snippet starters.

### 8. Exam Sentences Pass

Create a dedicated section with midterm/final-style answer sentences:
- One sentence per major concept, phrased as if writing an exam answer.
- Generic answer frames for:
  - "Evaluate a channel" → funnel stage + objective + key metric + risk
  - "Recommend an action" → diagnose → isolate variable → A/B test → measure
  - "Assess sustainability" → calculate CAC & LTV → check 3:1 ratio

### 9. Master Formula Table

Create a consolidated table with ALL formulas used in the course:

| Metric | Formula | What It Tells You |
|--------|---------|-------------------|
| CTR | Clicks / Impressions × 100 | Ad appeal |
| CPC | Total Cost / Clicks | Avg price per click |
| CPA | Total Cost / Conversions | Cost per acquisition |
| ROAS | Revenue / Ad Cost | Profitability per ad dollar |
| Conv. Rate | Conversions / Visitors × 100 | Funnel efficiency |
| CAC | Mktg+Sales Cost / New Customers | Cost to win one customer |
| LTV | AOV × Frequency × Lifespan | Total customer worth |
| LTV:CAC | LTV / CAC | Sustainability (target ≥ 3) |
| Gross Margin | (Revenue – COGS) / Revenue × 100 | Markup power |

### 10. Index Box

At the top of the document (before columns), add a dark-blue index block
listing every section with a jump label. This lets the user flip the printed
pages and find sections instantly.

### 11. Compile & Verify

```bash
typst compile cheatsheet-lec.typ
```

- Fix syntax issues (unclosed brackets, math token errors).
- If `$` appears in text, escape or replace with "USD".
- Ensure no key section regressions after rewrite.

## Typst Column Layout Selection

Choose based on user preference:

- **2-column** (readability-first): margin 0.45cm, font 7pt, gutter 0.5em
- **3-column** (balanced density): margin 0.35cm, font 6.6pt, gutter 0.38em ← DEFAULT
- **4-column** (max density): margin 0.25cm, font 6.1pt, gutter 0.30em

When changing columns, preserve section order and high-yield blocks first.

## Typst Macros (Keep Centralised)

```typst
#let section-box(title, color, body) = {
  block(width: 100%, breakable: true, stroke: 0.45pt + color, radius: 3pt, [
    #block(width: 100%, fill: color, inset: 2.6pt,
      text(fill: white, weight: "bold", size: 7pt, title))
    #block(width: 100%, inset: 3pt, body)
  ])
}

#let subheader(color, body) = {
  text(fill: color, weight: "bold", size: 6.8pt, body)
}

#let formula(body) = {
  box(fill: rgb("#e8f4f8"), inset: 2pt, radius: 2pt,
    width: 100%, text(size: 6.8pt, weight: "medium", body))
}

#let speed(body) = {
  box(fill: rgb("#d4edda"), inset: 2pt, radius: 2pt,
    width: 100%, text(size: 6.4pt, body))
}

#let warn(body) = {
  box(fill: rgb("#f8d7da"), inset: 2pt, radius: 2pt,
    width: 100%, text(size: 6.4pt, body))
}

#let index-box(body) = {
  block(width: 100%, breakable: true,
    stroke: 0.6pt + rgb("#1a237e"), radius: 4pt, [
    #block(width: 100%, fill: rgb("#1a237e"), inset: 3pt,
      text(fill: white, weight: "bold", size: 7.5pt,
        "INDEX — Jump to Section"))
    #block(width: 100%, inset: 4pt, body)
  ])
}
```

## Required Content Blocks

Unless the user opts out, ensure these exist:

1. Core concepts/definitions
2. Method-selection workflow (how to choose approach)
3. High-frequency templates/patterns
4. Edge cases/common mistakes
5. Speed tricks / option-elimination checklist
6. Tool quick-reference strip
7. Master formula table
8. Exam-ready sentence templates
9. Visual diagrams (funnels, pyramids, flows, dashboards)
10. Printable index / navigation box

## Domain Overlays (Apply Selectively)

### Business / Marketing Analytics Overlay
- KPI glossary (CAC, LTV, ROAS, churn, retention, conversion)
- Funnel stage mapping for each channel
- Budget allocation % table by business stage
- Financial fluency flow: Revenue → COGS → Gross Profit → Op. Income → Net Income

### Data / Tech Overlay
- Attribution model comparison table (First/Last/Linear/Time-Decay)
- Cross-device tracking types (Deterministic / Probabilistic / Hybrid)
- Data tiers dashboard (1st / 2nd / 3rd party)
- RTB ecosystem flow diagram

### General Exam Overlay
- Generic answer frames for open-ended questions
- Elimination checklist (MECE, LTV:CAC > 3:1, data tier, risk shift)
- Diagnostic trees (low traffic → high CAC)

## Output Report Structure

After creating the cheatsheet, report to the user:

1. What changed (by file)
2. Coverage added (bullet list of concrete topics/patterns)
3. Density/layout changes (margins/font/gutters/breakability)
4. Speed tricks retained or improved
5. Compile result and warnings
6. Optional next step

## Common Pitfalls

- **Unclosed delimiters in Typst:** The `[` and `]` in `#columns()[...]` and `#section-box()[...]` must balance. When nesting boxes or tables inside column blocks, count brackets carefully.
- **Math token errors:** The `$` character is special in Typst. Replace monetary symbols with "USD" or use `#text()[...]` wrappers.
- **Font warnings:** If "Linux Libertine" is not installed, Typst falls back to a default. Use "New Computer Modern" as a safe alternative.
- **Breakable sections:** Always set `breakable: true` on `block()` and `section-box()` to prevent large empty regions when content spans columns.
