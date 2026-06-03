# Typst Templates for Cheatsheets

Copy-paste snippets for building `.typ` cheat sheets. Keep style tokens centralized so layout changes are low-risk.

## Column Layout Starters

### Variation A: 2-column (readability-first)

```typst
#set page(paper: "a4", margin: 0.45cm, flipped: true)
#set text(font: "New Computer Modern", size: 7pt)
#set par(leading: 0.6em, justify: true)

#let section-box(title, color, body) = {
  block(width: 100%, breakable: true, stroke: 0.5pt + color, radius: 4pt, [
    #block(width: 100%, fill: color, inset: 3pt,
      text(fill: white, weight: "bold", title))
    #block(width: 100%, inset: 4pt, body)
  ])
}

#columns(2, gutter: 0.5em)[
  #section-box("Core Concepts", rgb("#8e44ad"))[ ... ]
  #v(0.2em)
  #section-box("Methods", rgb("#3498db"))[ ... ]
]
```

### Variation B: 3-column (balanced density) — DEFAULT

```typst
#set page(paper: "a4", margin: 0.35cm, flipped: true)
#set text(font: "New Computer Modern", size: 6.6pt)
#set par(leading: 0.55em, justify: true)

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

#index-box[
  #grid(columns: (1fr, 1fr, 1fr), gutter: 0.3em,
    [§1 Topic A], [§2 Topic B], [§3 Topic C]
  )
]

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
#set text(font: "New Computer Modern", size: 6.1pt)
#set par(leading: 0.5em, justify: true)

#let section-box(title, color, body) = {
  block(width: 100%, breakable: true, stroke: 0.42pt + color, radius: 3pt, [
    #block(width: 100%, fill: color, inset: 2.3pt,
      text(fill: white, weight: "bold", size: 6.9pt, title))
    #block(width: 100%, inset: 2.6pt, body)
  ])
}

#columns(4, gutter: 0.30em)[
  #section-box("Core", rgb("#9b59b6"))[ ... ]
  #v(0.08em)
  #section-box("Methods", rgb("#8e44ad"))[ ... ]
]
```

## Diagram Patterns

### Funnel (Stacked Boxes)

```typst
#align(center)[
  #box(fill: rgb("#3498db"), inset: 4pt, radius: 2pt,
    text(fill: white, weight: "bold", size: 7pt, "AWARENESS"))
  #text(size: 6pt)[↓]
  #box(fill: rgb("#2ecc71"), inset: 4pt, radius: 2pt,
    text(fill: white, weight: "bold", size: 7pt, "ENGAGEMENT"))
  #text(size: 6pt)[↓]
  #box(fill: rgb("#e74c3c"), inset: 4pt, radius: 2pt,
    text(fill: white, weight: "bold", size: 7pt, "CONVERSION"))
]
```

### Pyramid (Minto Style)

```typst
#align(center)[
  #box(fill: rgb("#3498db"), inset: 4pt, radius: 2pt,
    text(fill: white, weight: "bold", size: 6.5pt, "L1: KEY MESSAGE"))
  #text(size: 6pt)[↓]
  #grid(columns: (1fr, 1fr, 1fr), gutter: 0.3em,
    [#box(fill: rgb("#5dade2"), inset: 3pt, radius: 2pt, text(size: 6pt, "Point 1"))],
    [#box(fill: rgb("#5dade2"), inset: 3pt, radius: 2pt, text(size: 6pt, "Point 2"))],
    [#box(fill: rgb("#5dade2"), inset: 3pt, radius: 2pt, text(size: 6pt, "Point 3"))]
  )
  #text(size: 6pt)[↓]
  #box(fill: rgb("#aed6f1"), inset: 3pt, radius: 2pt,
    text(size: 6pt, "L3: Evidence (data, testimonials)"))
]
```

### Horizontal Flow (Ecosystem)

```typst
#align(center)[
  #text(size: 6.5pt, weight: "bold")[Advertiser]
  #text(size: 6.5pt)[ → DSP → Ad Exchange ← SSP ← Publisher]
  #text(size: 6pt)[↑ DMP (data) ↑]
]
```

### Comparison Table

```typst
#table(columns: (1fr, 1fr, 1fr), inset: 2pt, stroke: 0.3pt,
  [*Owned*], [*Paid*], [*Earned*],
  [You control], [You rent], [Others give],
  [Website, blog], [Ads, SEM], [Reviews, PR]
)
```

### KPI Dashboard

```typst
#table(columns: (1.5fr, 0.8fr, 0.8fr, 0.8fr, 0.5fr),
  inset: 2pt, stroke: 0.3pt, align: (left, center, center, center, center),
  [*Metric*], [*Jan*], [*Feb*], [*Mar*], [*Trend*],
  [Visitors], [12,500], [14,200], [16,000], [↑],
  [Conv. Rate], [1.84%], [2.07%], [1.94%], [↓]
)
```

## Formula Table

```typst
#table(columns: (1.5fr, 2fr, 1.5fr), inset: 2pt, stroke: 0.3pt,
  [*Metric*], [*Formula*], [*Meaning*],
  [CTR], [Clicks / Impressions × 100], [Ad appeal],
  [CPC], [Total Cost / Clicks], [Price per click],
  [CPA], [Total Cost / Conversions], [Cost per acquisition],
  [ROAS], [Revenue / Ad Cost], [Profitability],
  [CAC], [Mktg Cost / New Customers], [Acquisition cost],
  [LTV], [AOV × Freq × Lifespan], [Customer worth],
  [Gross Margin], [(Rev – COGS) / Rev × 100], [Markup %]
)
```

## Syntax Safety Rules

1. **Balance brackets:** Every `[` in `#columns()[` and `#section-box()[` must close. Count them when nesting tables inside boxes.
2. **Escape `$`:** Typst uses `$` for math mode. Replace monetary `$` with "USD" or wrap in `#text()[...]`.
3. **Breakable blocks:** Always set `breakable: true` on blocks that may span columns.
4. **Font fallback:** If "Linux Libertine" is missing, use "New Computer Modern" or "DejaVu Serif".
