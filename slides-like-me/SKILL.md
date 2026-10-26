---
name: slides-like-me
description: Create or revise presentations in the user's preferred restrained style, routing to Slidev or editable PowerPoint. Use for personal slide creation or redesign; ask for the format only when the request leaves it unclear.
---

# Slides Like Me

Create a presentation that communicates the requested content in the user's
preferred style. Use this skill for design and format decisions, then load the
appropriate available presentation skill for implementation. If a named skill is
not installed, use an equivalent available workflow for the chosen format; do not
claim to have loaded an unavailable skill.

## Choose the format

- If the user requests Slidev, Markdown/Vue slides, or a Slidev project, use
  **slidev** and read its `SKILL.md` before implementation.
- If the user requests PowerPoint, PPT, PPTX, or an editable PowerPoint deck,
  use **pptx** and read its `SKILL.md` before implementation. Use the available
  **presentations** skill when needed for its supported creation or editing tools.
- When revising an existing deck, keep its format unless the user requests a
  conversion. An explicit format choice in the current conversation also counts.
- If neither format is established, ask one concise question: "Would you like
  this in Slidev or editable PowerPoint (.pptx)?" Wait for the answer before
  building the deck. Continue useful content review while waiting.
- If the user explicitly requests a different format, follow that request with
  the appropriate available workflow.

Do not ask again once the format is clear. PDF is an export format; if the user
requests only a PDF and no authoring format is established, ask the format question.

## Personal design preferences

Use a restrained, conventional Google Slides or Slidesgo-style presentation that
feels less AI-designed. Let the content determine the layout. Use clear hierarchy,
readable typography, consistent alignment, and purposeful whitespace.

Include only text and visual elements that communicate the requested content or
help the audience understand it. Do not add gratuitous text, decorative additions,
or eyebrow elements (small labels above headings). Avoid filler subtitles,
slogans, generic takeaways, and repeated labels added just to fill a layout.
A simple text slide is acceptable when the content calls for it; do not add an
icon, card, shape, or image solely to satisfy a decoration quota.

Keep each point distinct. Remove repeated claims and overlapping sections.
Use concise justifications and concrete evidence. For analytical or academic
slides, connect evidence to analysis and relevant theory, with claims supported
by the supplied sources. Keep the slide count, palette, fonts, and structure
appropriate to the current request; previous decks do not establish fixed values.

When tool-specific design defaults conflict with these personal preferences,
apply the personal preferences while retaining technical correctness and required
verification.

## Content and writing

Use the user's draft, brief, audience, timing, and source material to determine
scope. Ask only for missing information that materially affects the result.
Do not invent evidence, findings, quotations, citations, or requirements.

Use **write-like-me** when drafting substantial presentation prose on the user's
behalf, following its source-retrieval workflow. Explicit preferences in the
current request take precedence over inferred style. Keep fuller explanations in
speaker notes when notes are requested or useful, rather than crowding slides.

## Additional samples

Refer to [.context/](.context/) when available for additional samples of previous
work done by the user. Use these samples as references; the current request and
preferences take precedence. If `.context/` is absent, continue with this skill.

Keep `.context/` local and Git-ignored. Exclude its contents from commits,
shared packages, and published output.

## Build, inspect, and deliver

Follow the selected implementation skill's build and verification workflow.
Inspect the rendered slides and any requested export for clipping, overlap,
unreadable text, broken images, and missing content. Fix observed issues before
delivery. Review every element for a clear purpose and remove gratuitous copy,
decoration, repeated content, and eyebrow elements.

For editable PowerPoint, use native text and shapes where practical. Do not
flatten whole slides into PNGs as a substitute for editable output. Preserve
necessary evidence screenshots and image assets as images.

Deliver the requested source or editable deck and any requested exports. State
material limitations, including export fidelity issues, plainly. Publish, upload,
or send the deck elsewhere only when the user requests it.
