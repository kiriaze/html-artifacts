---
type: html-artifacts-config
project: YOUR_PROJECT_NAME
---

# HTML Knowledge Hub — Config

Copy this file to `docs/html/config.md` in your project. The `html-artifacts` skill reads it
before generating any HTML artifact. Fill in your project's values; delete rows that don't apply.

---

## Design Tokens

Override the skill's clean-white defaults with your project's palette. If you don't need custom
tokens, delete this section — the skill will use clean white defaults automatically.

```css
/* Backgrounds */
--bg:           #ffffff;   /* Page background */
--bg-subtle:    #f9fafb;   /* Cards, section backgrounds */
--bg-subtle-2:  #f3f4f6;   /* Nested cards, code blocks */

/* Text */
--fg:           #111827;   /* Primary text */
--fg-muted:     #6b7280;   /* Secondary text, captions */
--fg-faint:     #9ca3af;   /* Placeholder, timestamps */

/* Accent — your primary brand color */
--accent:       #2563eb;   /* Links, highlights, buttons */
--accent-bg:    #eff6ff;   /* Accent tint backgrounds */
--accent-border:#bfdbfe;   /* Accent tint borders */

/* Semantic colors — usually leave as-is */
--success:      #16a34a;
--success-bg:   #f0fdf4;
--warning:      #d97706;
--warning-bg:   #fffbeb;
--danger:       #dc2626;
--danger-bg:    #fef2f2;

/* Borders */
--border:       #e5e7eb;
--border-strong:#d1d5db;

/* Typography */
--font-sans:    -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif;
--font-mono:    'SF Mono', 'Cascadia Code', Menlo, Monaco, 'Courier New', monospace;

/* Shape */
--radius-sm:    4px;
--radius-md:    6px;
--radius-lg:    10px;
--shadow-sm:    0 1px 2px rgba(0,0,0,0.05);
--shadow-md:    0 4px 6px rgba(0,0,0,0.07), 0 1px 2px rgba(0,0,0,0.05);
```

Spacing: 4pt scale only — `4, 8, 12, 16, 24, 32, 48, 64px`. No arbitrary values.

---

## Terminology

Add your project's key terms so the skill uses consistent language in generated docs.

| Term | Meaning |
|---|---|
| [Product name] | [One-clause description] |
| [Key concept 1] | [Definition] |
| [Key concept 2] | [Definition] |

---

## Active HTML Docs

List every HTML file the skill manages. Used by `html-artifacts audit` and `html-artifacts refresh`.

| File | Source | Pattern | Description |
|---|---|---|---|
| `index.html` | — | hub | Navigation hub |

---

## Recurring Artifact Directories

Artifact types that accumulate over time go here:

| Directory | Pattern | Naming |
|---|---|---|
| `reports/` | status-report | `YYYY-MM-DD.html` |
| `reviews/` | code-review | `PR-NNN.html` |
| `incidents/` | incident-report | `YYYY-MM-DD.html` |

---

## Refresh Rules

| Source file | HTML doc | Notes |
|---|---|---|
| `docs/plans/PLAN.md` | `plan.html` | |

Add one row per source→doc pair. Run `html-artifacts audit` to check freshness.
