# html-artifacts

A Claude Code skill (and multi-agent compatible rule set) that generates self-contained, interactive HTML files instead of Markdown walls for plans, competitor analysis, architecture diagrams, code reviews, reports, and more.

Inspired by [ThariqS/html-effectiveness](https://github.com/ThariqS/html-effectiveness) — the argument that spatial information, relationships, and structure are better _shown_ than described. An interactive HTML page understood in 5 seconds beats a Markdown wall nobody re-reads.

---

## Install

**Option A — clone directly into your skills directory:**

```bash
git clone https://github.com/kiriaze/html-artifacts ~/.agents/skills/html-artifacts

# Symlink for Claude Code (if your skills live in ~/.agents/skills/)
ln -s ~/.agents/skills/html-artifacts ~/.claude/skills/html-artifacts
```

**Option B — dotfiles / submodule:**

```bash
# Inside your dotfiles repo
git submodule add https://github.com/kiriaze/html-artifacts skills/html-artifacts
ln -s ~/dotfiles/skills/html-artifacts ~/.claude/skills/html-artifacts
```

Claude Code auto-discovers skills in `~/.claude/skills/` at session start.

---

## Invoke

In any Claude Code session:

```
Use the html-artifacts skill to generate a competitor analysis for [product].
```

Or via slash command if registered:

```
/html-artifacts
```

The skill is invoked via the `Skill` tool internally — you don't need to do anything special once it's installed.

---

## Decision tree

Before generating any documentation output, apply this tree:

```
Plan, analysis, report, diagram, or explainer?       → HTML  (use this skill)
Reviewed as a git diff (HANDOFF.md, AGENT_TASKS.md)? → Markdown
Embedded in GitHub (README, PR body, issue)?         → Markdown
Commit message, inline comment, or chat reply?       → plain text
Output is < 10 lines?                                → Markdown or plain
Consumed programmatically (JSON, config, build)?     → keep native format
```

**Always Markdown (never convert):** `HANDOFF.md`, `AGENT_TASKS.md`, `CLAUDE.md`, `AGENTS.md`, `README.md`, `CHANGELOG.md`, `.github/` templates, config files.

---

## 18 patterns

| Pattern                      | Invoke when                             | Key features                                                                           |
| ---------------------------- | --------------------------------------- | -------------------------------------------------------------------------------------- |
| `product-brief`              | Product/feature plan, strategy doc      | Sticky nav, personas grid, CSS data-flow diagram, roadmap timeline, risk table         |
| `comparison-board`           | Competitor analysis, option evaluation  | Sortable feature matrix, 2×2 positioning map, acquisition targets                      |
| `code-review`                | PR writeup, diff explanation            | Margin annotations, severity tags, before/after blocks, annotation export to Claude    |
| `architecture-diagram`       | System overview, data flow              | CSS/SVG component boxes, hot-path highlight, click-to-expand responsibilities          |
| `status-report`              | Sprint recap, what shipped/slipped      | Metadata pill row, shipped cards, pure-CSS bar chart, slipped section, next steps      |
| `design-explorer`            | Component variants, color palette       | Click-to-copy swatches, live-editable typography specimens, component states           |
| `research-explainer`         | Feature docs, concept tutorial          | TL;DR chip, collapsible steps, tabbed config snippets, hover glossary, FAQ             |
| `editor-board`               | Triage board, backlog prioritization    | 4 columns (Now/Next/Later/Cut), click-chip filtering, drag-and-drop, copy as markdown  |
| `approach-explorer`          | Compare 2–4 implementation options      | Criteria grid, traffic-light summary row, expandable rationale, recommendation callout |
| `feature-spec`               | Single-feature spec before coding       | User stories with checkbox AC, edge cases table, open questions with status badges     |
| `milestone-plan`             | Product roadmap with milestones         | SVG completion rings, dependency connectors, phase bands, shipped/deferred states      |
| `incident-report`            | Post-mortem after an outage             | Timeline ruler, 5-whys drill-down, impact table, action items with owners              |
| `flowchart-diagram`          | Decision trees, process flows           | CSS+SVG nodes/arrows, node type legend, click-to-expand detail panel                   |
| `slide-deck`                 | Presentation, pitch, walkthrough        | Full-viewport slides, keyboard nav, slide types, speaker notes, progress bar           |
| `prototype-interaction`      | Clickable UI mockup for flow validation | State machine panels, history.pushState, annotation sticky notes, export to Claude     |
| `exploration-visual-designs` | Design variants before committing       | Variant tab strip, device frame, annotation layer, export annotations to Claude        |
| `prototype-animation`        | CSS animation timing exploration        | Live duration/easing editor, cubic-bezier visualizer, keyframe table, copy CSS         |
| `editor-feature-flags`       | Planning feature flags before coding    | Toggle + rollout % slider, targeting rules, kill switch, copy as JSON                  |

See `SKILL.md` for the full specification of each pattern including required structure, security rules, and file size budget.

---

## Project setup (per project)

### 1. Copy the config template

```bash
mkdir -p docs/html
cp ~/.agents/skills/html-artifacts/templates/config.md docs/html/config.md
```

Edit `docs/html/config.md` to set your project's design tokens, terminology, and the list of HTML docs you want to maintain.

### 2. Wire up the stale-doc hook

Pick the hook for your AI tool:

**Claude Code** — merge `hooks/claude-settings-snippet.json` into your `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "file=$(echo \"$CLAUDE_TOOL_INPUT\" | python3 -c \"import sys,json; d=json.load(sys.stdin); print(d.get('file_path',''))\" 2>/dev/null); if [[ \"$file\" == */docs/plans/*.md || \"$file\" == */docs/architecture.md ]]; then echo \"\\u26a0  html-artifacts: $file changed — run 'html-artifacts refresh' to update docs/html/\"; fi"
          }
        ]
      }
    ]
  }
}
```

**Codex / OpenAI agents** — add the contents of `hooks/codex-agents-snippet.md` to your `AGENTS.md`.

**Gemini CLI** — add the contents of `hooks/gemini-md-snippet.md` to your `GEMINI.md`.

### 3. Generate your first doc

```
Use html-artifacts to generate a product-brief for [your product] based on [source file].
```

The skill writes the file, embeds a `<!-- generated: ISO-timestamp | source: path -->` comment, and offers to update `docs/html/index.html`.

---

## Keeping docs fresh

Every generated file has a `<!-- generated: ISO-timestamp | source: path -->` comment on line 1.

```
# Check which docs are stale
html-artifacts audit

# Regenerate stale docs interactively
html-artifacts refresh
```

The audit mode compares each file's embedded timestamp against the source file's last git commit date (`git log -1 --format=%cI -- <path>`).

---

## Examples

The `examples/` directory contains one reference output per pattern, all using fictional "Acme Sync" content:

| File                                 | Pattern              |
| ------------------------------------ | -------------------- |
| `examples/product-brief.html`        | product-brief        |
| `examples/comparison-board.html`     | comparison-board     |
| `examples/code-review.html`          | code-review          |
| `examples/architecture-diagram.html` | architecture-diagram |
| `examples/status-report.html`        | status-report        |
| `examples/design-explorer.html`      | design-explorer      |
| `examples/research-explainer.html`   | research-explainer   |
| `examples/editor-board.html`         | editor-board         |

Open any example directly in your browser — no server needed.

---

## Design system defaults

The skill ships with a neutral warm palette that works for most products. Override any token via `docs/html/config.md`.

| Token          | Default         | Use                        |
| -------------- | --------------- | -------------------------- |
| `--bg`         | `#FAF9F5`       | Page background            |
| `--fg`         | `#141413`       | Primary text               |
| `--accent`     | `#D97757`       | Links, highlights, buttons |
| `--secondary`  | `#E3DACC`       | Cards, section backgrounds |
| `--tertiary`   | `#788C5D`       | Tags, secondary accents    |
| `--font-serif` | Georgia         | Headings                   |
| `--font-sans`  | System UI       | Body text                  |
| `--font-mono`  | SF Mono / Menlo | Code                       |

Spacing: 4pt scale (`4, 8, 12, 16, 24, 32, 48, 64px`). No arbitrary values.

---

## Security rules

All generated HTML must follow these rules (enforced by the skill spec):

1. No `eval()` — ever.
2. No `innerHTML` set from external or user-controlled input.
3. No `fetch()` to external URLs — all data is embedded.
4. No `<script src="...">` from CDNs — zero external dependencies.
5. Use `textContent` for dynamic text; `createElement` + `appendChild` for dynamic elements.
6. Event handlers must be function references via `addEventListener`, not inline strings.

---

## Adding a new pattern

1. Identify a real output type you're producing as Markdown that would be better as HTML.
2. Name it (`verb-noun` format: `incident-report`, `exploration-approaches`, `flowchart-diagram`).
3. Add an entry to the **8 Output Patterns** table in `SKILL.md` with: trigger, file convention, and 4–5 required structural elements.
4. Generate one real example and save it in `examples/`.
5. Open a PR with both the `SKILL.md` update and the example file.

Patterns to consider next (not yet in the skill): `exploration-approaches`, `incident-report`, `flowchart-diagram`, `slide-deck`, `prototype-interaction`.

---

## File size budget

- Target: ≤ 600 lines per generated file.
- Prefer CSS transitions/animations over JS.
- Inline SVGs over `<canvas>`.
- If content genuinely demands > 600 lines, split into separate files linked from `index.html`.

---

## Compatibility

| AI tool               | Skill support                                                                               | Hook support                                  |
| --------------------- | ------------------------------------------------------------------------------------------- | --------------------------------------------- |
| Claude Code (CC)      | Native — `Skill` tool auto-discovers `~/.claude/skills/`                                    | `PostToolUse` hook in `.claude/settings.json` |
| Codex / OpenAI agents | Reads `AGENTS.md` instruction — paste `hooks/codex-agents-snippet.md`                       | Rule-based (no process hooks)                 |
| Gemini CLI            | `activate_skill` tool — paste `hooks/gemini-md-snippet.md` into `GEMINI.md`                 | Rule-based                                    |
| Other agents          | Copy the decision tree and pattern table from `SKILL.md` into your agent's instruction file | Rule-based                                    |

---

## Related

- [ThariqS/html-effectiveness](https://github.com/ThariqS/html-effectiveness) — the original gallery that inspired this skill (20 standalone HTML demos)
- [dogum/html-artifacts](https://github.com/dogum/html-artifacts) — similar skill with Markdown carve-outs
- [clockless-org/html-anything](https://github.com/clockless-org/html-anything) — converts any file type to styled HTML
