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

Before generating any HTML artifact, resolve design tokens in this order:

1. **`docs/html/config.md`** — if it exists, read tokens, terminology, and active docs list from it. This is the authoritative override.
2. **Project design system** — if no config, scan for: `src/globals.css`, `src/styles/globals.css`, `tailwind.config.ts`, `tailwind.config.js`. Extract CSS custom properties or Tailwind color tokens if present.
3. **Clean white defaults** — if nothing is found, use the defaults below. Never fall back to warm/branded palettes without a project signal.

If generating an HTML artifact **from scratch** (no source `.md` file), also write a companion `.md` file with the same data in flat diffable format. The `.md` is the data source; the `.html` is presentation. Declare the `.md` as the `source:` in the generated comment.

---

## Default Design Tokens

Clean, minimal, white. Works for any project. Override via `docs/html/config.md`.

```css
--bg:           #ffffff;
--bg-subtle:    #f9fafb;
--bg-subtle-2:  #f3f4f6;
--fg:           #111827;
--fg-muted:     #6b7280;
--fg-faint:     #9ca3af;
--accent:       #2563eb;
--accent-bg:    #eff6ff;
--accent-border:#bfdbfe;
--success:      #16a34a;
--success-bg:   #f0fdf4;
--warning:      #d97706;
--warning-bg:   #fffbeb;
--danger:       #dc2626;
--danger-bg:    #fef2f2;
--border:       #e5e7eb;
--border-strong:#d1d5db;
--font-sans:    -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif;
--font-mono:    'SF Mono', 'Cascadia Code', Menlo, Monaco, 'Courier New', monospace;
--radius-sm:    4px;
--radius-md:    6px;
--radius-lg:    10px;
--shadow-sm:    0 1px 2px rgba(0,0,0,0.05);
--shadow-md:    0 4px 6px rgba(0,0,0,0.07), 0 1px 2px rgba(0,0,0,0.05);
```

Spacing: 4pt scale (`4, 8, 12, 16, 24, 32, 48, 64px`). No arbitrary values.

Body: `font-size: 15px; line-height: 1.6; -webkit-font-smoothing: antialiased;`

**Dark mode** — every generated file includes a `html.dark {}` override block and a `◐` toggle button in the nav. localStorage key: `'ha-theme'`. Respects `prefers-color-scheme` on first load.

```css
html.dark {
  --bg:#0d1117; --bg-subtle:#161b22; --bg-subtle-2:#21262d;
  --fg:#e6edf3; --fg-muted:#8b949e; --fg-faint:#6e7681;
  --accent:#58a6ff; --accent-bg:#0c2d6b; --accent-border:#1f6feb;
  --success:#3fb950; --success-bg:#0f2c1a;
  --warning:#d29922; --warning-bg:#2d1f00;
  --danger:#f85149; --danger-bg:#3d0000;
  --border:#30363d; --border-strong:#484f58;
}
```

---

## Recurring Artifacts — Directory Convention

For artifact types that repeat over time, use dated subdirectories instead of overwriting:

| Pattern | Path convention |
|---|---|
| `status-report` | `docs/html/reports/YYYY-MM-DD.html` |
| `code-review` | `docs/html/reviews/PR-NNN.html` |
| `incident-report` | `docs/html/incidents/YYYY-MM-DD.html` |

The `index.html` hub lists each subdirectory as a collapsible section with a timeline of entries, not individual cards.

---

## Output Patterns

### 1. `product-brief`
**Trigger:** Product/feature plan, strategy doc, spec
**File:** `docs/html/plan.html`
**Structure:**
- Sticky top nav with anchor links: Overview · Personas · Architecture · Roadmap · Risks
- Overview: tagline, value prop, current state summary, free/pro pricing
- Personas: responsive card grid with role, core need, pain point
- Architecture: CSS data-flow diagram (boxes + directional connectors), no external SVG libs
- Roadmap: horizontal timeline with milestone markers (shipped / next / deferred)
- Risks: table with likelihood × impact badges and mitigation column
- Collapsible `<details>` for secondary depth
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html` in nav or footer

### 2. `comparison-board`
**Trigger:** Competitor analysis, option evaluation, feature matrix
**File:** `docs/html/competitor-analysis.html`
**Structure:**
- Header: 1–2 sentence positioning statement + "Verified [date]" chip
- Our Advantages: row of green-tinted chips
- Sortable feature matrix: click column header toggles sort; our product column pinned + tinted
- Cell color coding: ✓ success / ~ warning / ✗ danger (icon + bg, icon centered in cell)
- 2×2 positioning map: CSS-only, percentage-based chip placement, labeled axes
- Acquisition Targets: numbered cards with user count, pain point, messaging hook
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 3. `code-review`
**Trigger:** PR writeup, diff explanation, change summary
**File:** `docs/html/reviews/PR-NNN.html`
**Structure:**
- PR header: title, author, files changed, motivation
- Severity legend: NOTE / WARN / CRITICAL with color codes
- File-by-file tour: before/after code blocks, click-to-expand annotation bubbles in margin
- Jump links per file section in sticky nav
- Review focus callout at bottom
- **Annotation export:** "Copy annotations as Markdown" button serializes all open annotation bubbles as structured text for pasting back to Claude to iterate on the review
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 4. `architecture-diagram`
**Trigger:** System overview, data flow, entrypoint map
**File:** `docs/html/architecture.html`
**Structure:**
- CSS-grid component diagram with labeled directional connectors (unicode arrows or thin borders)
- Hot-path components highlighted with dashed accent border
- Click any box to expand its responsibilities inline (toggle, not modal)
- Entrypoints table: name / file path / role / key responsibilities
- State layers section: visual stack showing state layers with explanatory text
- Constraints table: constraint / severity / impact / mitigation
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 5. `status-report`
**Trigger:** Sprint/weekly recap, what shipped / what slipped
**File:** `docs/html/reports/YYYY-MM-DD.html`
**Structure:**
- **Metadata row** at top: [X PRs Merged] [X Deploys] [X Incidents] [coverage delta] as pill chips
- **Highlights** section: 2–3 sentence narrative of the period
- **Shipped** section: items with category tags, PR/commit reference, brief context sentence (not just a title)
- **Velocity** chart: pure-CSS bar chart (bars as `height` on flex children), adequate spacing, no text overlap
- **Slipped** section: items with reason; bullet list uses `display:flex; align-items:center` — no `margin-top` on bullets
- **Next steps** checklist: `<input type="checkbox">` items, no JS
- Key metric callout: 1–2 numbers that tell the story of the period
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html` in header

### 6. `design-explorer`
**Trigger:** Component variants, color palette, design token review
**Structure:**
- Color swatch grid: click-to-copy hex, "Copied!" flash overlay
- Typography scale: specimens at 5 sizes, `contenteditable="true"` so text is editable live
- Component states: left-to-right (default / hover / active / disabled / error)
- Spacing scale: visual ruler with labeled bars
- Copy buttons on all code blocks
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 7. `research-explainer`
**Trigger:** Feature documentation, concept tutorial, how-it-works
**Structure:**
- TL;DR chip with left accent border at top
- Collapsible `<details>` steps, first open by default
- Tabbed code/config snippets (vanilla JS tab switching via `data-tab` attributes)
- Glossary: hover-highlighted rows with inline definitions
- FAQ: `<details>` with Q/A styling
- Smooth scroll to anchors (CSS `scroll-behavior: smooth` on `html`)
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html` in header

### 8. `editor-board`
**Trigger:** Triage board, backlog prioritization, sprint planning
**File:** `docs/html/triage/YYYY-MM-DD.html` (or named by initiative)
**Structure:**
- **Header:** board name, date, brief paragraph explaining the triage (what it covers, how to use it)
- **Filter bar:** `<select>` dropdowns for Priority / Assignee / Tag / Points; Reset CTA (type="button") clears all; "X cards hidden" chip appears when active; clicking any card chip (priority badge, tag, assignee, points) toggles that filter — visual outline on active chips
- **4 columns:** Now / Next / Later / Cut — `draggable="true"` + vanilla JS drag-and-drop; live item count badge per column; `.drag-over` dashed accent outline on drop target
- **Cards:** ticket number (monospace), title, priority badge (P0=danger / P1=warning / P2=accent / P3=subtle), multi-tags, assignee initials circle, point estimate badge (right-aligned), drag handle (⠿); cards hidden when filtered (`display:none`)
- **"Copy as Markdown"** button: generates a 4-column table of current board state (all columns including Cut) and writes to clipboard
- No external JS; vanilla DOM only; no `eval`, no external `innerHTML`
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

---

### 9. `approach-explorer`
**Trigger:** Comparing 2–4 implementation options before committing
**File:** `docs/html/approaches/YYYY-MM-DD-topic.html`
**Structure:**
- Header: decision question as `<h1>`, context paragraph, decision-by date chip
- Side-by-side criteria grid: rows = criteria (complexity, performance, cost, DX), columns = options; cell color-coded ✓ success / ~ warning / ✗ danger (icon + bg)
- Traffic-light summary row: green / yellow / red pill per option, sticks to bottom of grid
- Click any cell to expand inline rationale (toggle, no modal)
- Recommendation callout: chosen option with one-paragraph justification, accent left border
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 10. `feature-spec`
**Trigger:** Detailed spec for a single feature before implementation
**File:** `docs/html/specs/FEAT-NNN.html`
**Structure:**
- Header: feature name, author chip, status chip (Draft / Ready / Shipped), linked ticket (monospace)
- TL;DR chip with left accent border
- User stories: `<details>` per persona, each with `<input type="checkbox">` acceptance criteria
- Edge cases table: scenario / expected behavior / notes
- Open questions: numbered list with Open / Resolved status badges; resolved items struck through
- Out of scope: compact bulleted list in `--bg-subtle` card
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 11. `milestone-plan`
**Trigger:** Product roadmap with milestones, phases, and dependencies
**File:** `docs/html/roadmap.html`
**Structure:**
- Horizontal timeline: milestone columns with date labels; phase bands as color-coded background strips
- Each milestone card: title, completion ring (inline SVG `<circle>` with `stroke-dasharray` for %), status chip (Shipped / In Progress / Planned / Deferred)
- Shipped items rendered with success color + checkmark; deferred items in `--fg-faint`
- Dependency connectors: thin dashed `border-top` or SVG `<line>` between related milestones
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 12. `incident-report`
**Trigger:** Post-mortem after an outage, data loss, or security incident
**File:** `docs/html/incidents/YYYY-MM-DD.html`
**Structure:**
- Severity banner at top: P0 / P1 / P2 / P3 badge with color, duration chip, affected users chip
- Timeline ruler: vertical line with timestamped events; each event is a `<details>` (what happened, who noticed, action taken)
- 5-whys section: collapsible `<details>` chain — each Why links visually to the next
- Impact table: system / severity / user-facing effect columns
- Action items table: description / owner initials chip / due date / status badge (Open / Done)
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 13. `flowchart-diagram`
**Trigger:** Decision trees, process flows, state machines
**File:** `docs/html/flows/TOPIC.html`
**Structure:**
- CSS + inline SVG node-and-arrow diagram (no external libs): nodes as `<div>` absolutely positioned within a relative container; arrows as SVG `<line>` + `<marker>` arrowheads overlaid
- Node types: terminal (pill shape), process (rectangle), decision (diamond via CSS `clip-path` or rotated square), note (dashed border, `--fg-faint` text)
- Click any node to expand an inline detail panel (toggle below the diagram)
- Legend: node type key with small examples
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 14. `slide-deck`
**Trigger:** Presentation, pitch, or sequential walkthrough
**File:** `docs/html/decks/TOPIC.html`
**Structure:**
- Full-viewport slides (`100vw` × `100vh`), click or ← → keys to advance; swipe on touch
- Progress bar: thin accent line at top, width = `(current/total) * 100%`
- Slide types: cover (large headline + subtitle + date), content (heading + bullets), code (syntax-highlighted `<pre>`), comparison (2-column flex), quote (large centered pullquote)
- Slide counter bottom-right: `N / total` in `--fg-faint`
- Speaker notes: hidden `<aside>` per slide; `?` key toggles a floating notes panel
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link accessible from the first and last slide only

### 15. `prototype-interaction`
**Trigger:** Clickable UI mockup to validate flows before coding
**File:** `docs/html/prototypes/TOPIC.html`
**Structure:**
- State machine: HTML panels for each screen state; only one visible at a time (`display:none` on others)
- Clickable elements carry `data-next-state` attribute; JS transitions on click
- Browser back/forward via `history.pushState` so the back button works
- Annotation mode toggle: enables draggable sticky notes anywhere on the viewport
- **Export annotations:** "Copy annotations as Markdown" button serializes all notes (position, text, timestamp) for pasting back to Claude as structured feedback
- Device frame selector: mobile 375px / tablet 768px / desktop full-width CSS constraint toggle
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 16. `exploration-visual-designs`
**Trigger:** Multiple design variants of a UI screen to compare before committing
**File:** `docs/html/designs/TOPIC.html`
**Structure:**
- Variant switcher: horizontal tab strip (2–4 variants); active variant rendered below
- Full render of selected variant inside a CSS device frame (mobile / desktop toggle)
- Side-by-side toggle: shows all variants horizontally in a scrollable strip
- Annotation layer: click anywhere on a variant to drop a sticky note (draggable, scoped per variant)
- **Export annotations:** "Copy annotations as Markdown" button — pastes back to Claude as structured design feedback to iterate on
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 17. `prototype-animation`
**Trigger:** Exploring CSS animation timing, transitions, and keyframes before coding
**File:** `docs/html/animations/TOPIC.html`
**Structure:**
- Animation subject: a sample UI element (button, card, modal reveal) that plays the animation live
- Timing editor: `<input type="range">` for duration (0–3s, step 0.05), `<select>` for easing preset, custom cubic-bezier inputs; all update the subject live
- Easing visualizer: inline SVG cubic-bezier curve that redraws as easing changes
- Keyframe table: editable stops with property + value pairs (`contenteditable`)
- Before/after scrubber: drag to compare animated vs static state
- **Copy CSS:** exports the final `@keyframes` + `animation` declaration to clipboard
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

### 18. `editor-feature-flags`
**Trigger:** Planning feature flags before implementation — what flags exist, targeting rules, rollout strategy
**File:** `docs/html/flags/TOPIC.html`
**Structure:**
- Header: environment selector (Dev / Staging / Prod) as a tab strip; last-updated chip
- Flag list: each row has flag name (monospace), description, on/off toggle (`<input type="checkbox">`), rollout % (`<input type="range">`)
- Targeting rules per flag: collapsible `<details>` — segment chips (add/remove via text input), env chips, kill-switch checkbox
- Status badge: Active / Staged / Deprecated per flag
- **Copy as JSON:** exports the current flag state as a config JSON snippet to clipboard
- `<!-- generated: ISO-timestamp | source: path -->` on line 1
- Back-link to `index.html`

---

## Lower-Priority Patterns

No full spec yet. Add specs when a concrete use case emerges:

| Pattern | When | Key idea |
|---|---|---|
| `code-understanding` | Code archaeology, onboarding a new dev | Module dependency graph, call flow diagram, class hierarchy — CSS/SVG boxes, click to expand |
| `svg-illustrations` | Docs needing concept diagrams | Ad-hoc: generate a specific labeled SVG illustration; not a repeating pattern with a fixed structure |

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
    :root {
      /* tokens from config.md or defaults */
    }
    html.dark {
      --bg:#0d1117; --bg-subtle:#161b22; --bg-subtle-2:#21262d;
      --fg:#e6edf3; --fg-muted:#8b949e; --fg-faint:#6e7681;
      --accent:#58a6ff; --accent-bg:#0c2d6b; --accent-border:#1f6feb;
      --success:#3fb950; --success-bg:#0f2c1a;
      --warning:#d29922; --warning-bg:#2d1f00;
      --danger:#f85149; --danger-bg:#3d0000;
      --border:#30363d; --border-strong:#484f58;
    }
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--fg);
      font-family: var(--font-sans);
      font-size: 15px;
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
    }
    .dm-toggle {
      background: none; border: 1px solid var(--border);
      border-radius: var(--radius-sm); padding: 4px 8px;
      font-size: 13px; cursor: pointer; color: var(--fg-muted);
      line-height: 1; flex-shrink: 0;
    }
    .dm-toggle:hover { background: var(--bg-subtle); color: var(--fg); }
  </style>
</head>
<body>
  <nav>
    <a href="index.html">{Project Name}</a>
    <!-- anchor links -->
    <button class="dm-toggle" id="dm-toggle" type="button" title="Toggle dark mode">◐</button>
  </nav>
  <main><!-- content --></main>
  <script>
    /* Dark mode — must be first in script */
    (function(){
      var t=localStorage.getItem('ha-theme')||(window.matchMedia('(prefers-color-scheme:dark)').matches?'dark':'light');
      if(t==='dark')document.documentElement.classList.add('dark');
      document.getElementById('dm-toggle').addEventListener('click',function(){
        var d=document.documentElement.classList.toggle('dark');
        localStorage.setItem('ha-theme',d?'dark':'light');
      });
    })();
    /* Minimal vanilla JS only. */
    /* No eval. No innerHTML with external input. No fetch to external URLs. */
    /* Event handlers via addEventListener only. */
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
6. Event handlers must use `addEventListener`, not inline `onclick="..."` strings.

---

## File Size Budget

- Target: ≤ 600 lines per file
- Prefer CSS transitions over JS animations
- Inline SVGs over canvas
- If content genuinely demands > 600 lines, split into separate files linked from `index.html`

---

## Modes

### Default: generate an artifact
Invoked with output type and source content. Produce the file, report its path, offer to open in browser.

### `html-artifacts refresh`
1. Read `docs/html/config.md` for the active docs list
2. For each listed HTML file, read the `<!-- generated: -->` timestamp on line 1
3. Compare against source file's last git commit: `git log -1 --format=%cI -- {path}`
4. List stale files; regenerate on confirm

### `html-artifacts audit`
Print a table: filename / source / generated-at / source-modified-at / status (fresh / stale / unknown).

---

## Index File Convention

`docs/html/index.html` is the project navigation hub. It must:
- List every active HTML doc as a card: title, one-line description, last-generated date, freshness dot
- Recurring artifact types (reports/, reviews/) listed as collapsible timeline sections
- Green dot = fresh, amber dot = stale (computed from embedded timestamps)
- Regenerate the relevant card whenever any sibling doc is regenerated

---

## After Generating

1. Report the file path.
2. Offer to open it in the browser.
3. Update the corresponding card in `docs/html/index.html` if it exists.
4. Keep the `.md` source — HTML is always presentation, Markdown is always the diffable record.
