---
name: html-artifacts
description: Generate interactive self-contained HTML documents instead of Markdown for plans, competitor analysis, architecture diagrams, code reviews, and reports. Invoke when producing any rich planning or documentation artifact.
---

# html-artifacts

Produce self-contained, interactive HTML files for documentation, planning, and analysis outputs. No build step, no dependencies — opens directly in any browser.

**Core principle:** Spatial information, relationships, and structure are better *shown* than described. An interactive HTML page understood in 5 seconds beats a Markdown wall nobody re-reads.

---

## When to Use HTML

```
Output is a plan, brief, analysis, report, diagram, or explainer? → HTML
Output is meant to be opened in a browser?                        → HTML
Output reviewed as a git diff (HANDOFF.md, AGENT_TASKS.md)?      → Markdown (never HTML)
Output embedded in GitHub (README, PR body, issue)?               → Markdown
Output is a commit message, inline comment, or chat reply?        → plain text
Output < 10 lines?                                                → Markdown or plain
Output consumed by build tools or programmatically?               → JSON/YAML/Markdown
```

**Always Markdown (never HTML):**
- `HANDOFF.md`, `AGENT_TASKS.md`, `CLAUDE.md`, `AGENTS.md`
- `README.md`, `CHANGELOG.md`, any file GitHub renders
- `.github/PULL_REQUEST_TEMPLATE.md`, issue templates
- Config files (`.json`, `.yaml`, `.toml`)

---

## Project Config Discovery

Before generating any HTML artifact, check for `docs/html/config.md` in the project root. If it exists, read it to inherit:
- Design tokens (colors, fonts, radii)
- Terminology (product name, feature names)
- Active docs list (which HTML files exist and what they cover)

If no config exists, use the default design tokens below.

---

## Default Design Tokens

```css
--bg:       #FAF9F5;   /* Ivory */
--fg:       #141413;   /* Slate */
--accent:   #D97757;   /* Clay */
--secondary:#E3DACC;   /* Oat */
--tertiary: #788C5D;   /* Olive */
--g100:     #F5F4F0;
--g300:     #D1CFC8;
--g500:     #8A887F;
--g700:     #3D3C38;
--font-serif: Georgia, 'Times New Roman', serif;
--font-sans:  -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
--font-mono:  'SF Mono', Menlo, Monaco, 'Courier New', monospace;
--radius-sm:  4px;
--radius-md:  8px;
--radius-lg:  12px;
```

Spacing: 4pt scale (`4, 8, 12, 16, 24, 32, 48, 64px`). No arbitrary values.

---

## 8 Output Patterns

### 1. `product-brief`
**Trigger:** Product/feature plan, strategy doc, spec
**File:** `docs/html/plan.html`
**Structure:**
- Sticky top nav with anchor links: Overview · Personas · Architecture · Roadmap · Risks
- Overview tab: tagline, value prop, current state summary
- Personas: card grid with role, core need, pain point
- Architecture: visual data-flow with boxes/arrows (SVG or pure CSS grid)
- Roadmap: horizontal timeline with milestone markers
- Risks: table with likelihood × impact, mitigation notes
- Collapsible sections for secondary detail
- `<!-- generated: ISO-timestamp -->` comment at top

### 2. `comparison-board`
**Trigger:** Competitor analysis, option evaluation, feature matrix
**File:** `docs/html/competitor-analysis.html`
**Structure:**
- Header with summary positioning statement
- Sortable feature matrix table (click column header to sort)
- Pricing tier cards (free / pro / enterprise) per competitor
- Color-coded tiers: green (we win), yellow (parity), red (gap)
- Positioning callout box: our unique angle in 2 sentences
- `<!-- generated: ISO-timestamp -->` comment at top

### 3. `code-review`
**Trigger:** PR writeup, diff explanation, change summary
**Structure:**
- Motivation + summary at top
- File-by-file tour with before/after code blocks
- Margin annotations: severity tags (note / warn / critical)
- Jump links per file section
- Review focus guidance at bottom
- `<!-- generated: ISO-timestamp -->` comment at top

### 4. `architecture-diagram`
**Trigger:** System overview, data flow, entrypoint map
**File:** `docs/html/architecture.html`
**Structure:**
- SVG or CSS-grid diagram of components with labeled arrows
- Hot-path highlight (dashed border + accent color)
- Entrypoints listed in a sidebar panel
- Click a box to expand its responsibilities
- Constraints/tradeoffs section at bottom
- `<!-- generated: ISO-timestamp -->` comment at top

### 5. `status-report`
**Trigger:** Sprint recap, session summary, what shipped / what slipped
**Structure:**
- Shipped section: bullet list with PR links
- Slipped section: what moved and why
- Mini bar or sparkline chart (pure CSS)
- Next steps checklist (checkboxes, no JS required)
- `<!-- generated: ISO-timestamp -->` comment at top

### 6. `design-explorer`
**Trigger:** Component variants, color palette, design token review
**Structure:**
- Color swatch grid with hex + CSS var name, click to copy
- Typography scale specimens
- Component states laid out left-to-right (default / hover / active / disabled)
- Copy buttons on all code blocks
- `<!-- generated: ISO-timestamp -->` comment at top

### 7. `research-explainer`
**Trigger:** Feature documentation, concept tutorial, how-it-works
**Structure:**
- TL;DR chip at the top
- Collapsible step-by-step breakdown
- Tabbed code/config snippets (tab per environment)
- Hover-linked glossary (term → definition tooltip)
- FAQ section
- `<!-- generated: ISO-timestamp -->` comment at top

### 8. `editor-board`
**Trigger:** Triage board, feature flag toggles, prompt tuner
**Structure:**
- Drag-and-drop columns (Now / Next / Later / Cut) using HTML `draggable`
- Toggle switches with dependency warnings
- "Copy as Markdown" button exports current state
- No external JS; vanilla DOM only
- `<!-- generated: ISO-timestamp -->` comment at top

---

## HTML Template Skeleton

Every generated file must follow this skeleton:

```html
<!-- generated: {ISO-8601 timestamp} | source: {source file path} -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{Document Title} — {Project Name}</title>
  <style>
    /* CSS variables from config.md or defaults */
    :root { ... }
    /* Reset */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { background: var(--bg); color: var(--fg); font-family: var(--font-sans); ... }
    /* Layout and components */
    ...
  </style>
</head>
<body>
  <nav>...</nav>
  <main>...</main>
  <script>
    /* Minimal vanilla JS only. No eval, no innerHTML with external input, no fetch to external URLs. */
  </script>
</body>
</html>
```

---

## Security Rules (non-negotiable)

1. **No `eval()`** — ever.
2. **No `innerHTML`** set from user-controlled or external input.
3. **No `fetch()`** to external URLs — all data is embedded in the file.
4. **No `<script src="...">` from CDNs** — zero external dependencies.
5. Use `textContent` for dynamic text; `createElement` + `appendChild` for dynamic elements.
6. Event handlers must be function references, not strings: `onclick="fn()"` is banned; use `addEventListener`.

---

## File Size Budget

- Target: ≤ 600 lines per file
- Prefer CSS transitions over JS animations
- Inline SVGs over canvas; static data over fetched data
- If content genuinely demands > 600 lines, split into separate files and link from `index.html`

---

## Modes

### Default: generate an artifact
Invoked as `html-artifacts` with output type and source content. Produce the file and report its path.

### `html-artifacts refresh`
1. Read `docs/html/config.md` for the active docs list
2. For each listed HTML file, read the `<!-- generated: -->` comment to get its timestamp
3. Compare against the `source:` file's last-modified time via `git log -1 --format=%cI -- {path}`
4. List stale files (source newer than generated timestamp)
5. Ask which to regenerate; produce updated files on confirm

### `html-artifacts audit`
Read all `.html` files in `docs/html/`, extract their `<!-- generated: -->` and `source:` comments,
print a table: filename / source / generated-at / source-modified-at / status (fresh / stale / unknown).

---

## Index File Convention

`docs/html/index.html` is the navigation hub. It must:
- List every active HTML doc as a card with: title, one-line description, last-generated date
- Show a visual indicator (green dot = fresh, amber dot = stale) computed from embedded timestamps
- Be regenerated whenever any other HTML doc in `docs/html/` is regenerated

---

## After Generating

1. Report the file path.
2. Offer to open it in the browser.
3. If `docs/html/index.html` exists and is not the file just generated, offer to update its card for the new file.
4. Check if the `.md` source file should be kept or can be deprecated — default is **keep both**.
