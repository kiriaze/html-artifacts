---
type: html-artifacts-config
project: YOUR_PROJECT_NAME
---

# HTML Knowledge Hub — Config

Copy this file to `docs/html/config.md` in your project. The `html-artifacts` skill reads it
before generating any HTML artifact to pick up your project's design tokens, terminology,
and the list of active HTML docs.

---

## Design Tokens

Replace these with your project's values. The skill uses them as CSS variables in every
generated file so the output looks native to your product.

```css
--bg:        #FFFFFF;   /* Main background */
--fg:        #111111;   /* Primary text */
--accent:    #0066CC;   /* Primary accent — links, highlights, buttons */
--secondary: #F0F0F0;   /* Cards, section backgrounds */
--tertiary:  #4CAF50;   /* Secondary accents, tags, success states */
--g100:      #F8F8F8;   /* Lightest gray — hover states */
--g300:      #E0E0E0;   /* Borders */
--g500:      #888888;   /* Muted text */
--g700:      #444444;   /* Secondary text */
--font-serif:  Georgia, 'Times New Roman', serif;
--font-sans:   -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
--font-mono:   'SF Mono', Menlo, Monaco, 'Courier New', monospace;
--radius-sm:   4px;
--radius-md:   8px;
--radius-lg:   12px;
```

Spacing: 4pt scale. Use `4, 8, 12, 16, 24, 32, 48, 64px`. No arbitrary values.

---

## Terminology

Add your project's key terms so the skill uses the right vocabulary in generated docs.
Delete rows that don't apply; add as many as needed.

| Term | Meaning |
|---|---|
| [Product name] | [What the product is in one clause] |
| [Key concept 1] | [Definition] |
| [Key concept 2] | [Definition] |

---

## Active HTML Docs

List every HTML file the skill manages in this project. The skill checks these when
running `html-artifacts audit` and `html-artifacts refresh`.

| File | Source | Pattern | Description |
|---|---|---|---|
| `index.html` | — | hub | Navigation hub linking all docs |

---

## Refresh Rules

When any of these source files change, the corresponding HTML doc is stale:

| Source file | HTML doc |
|---|---|
| `docs/plans/PLAN.md` | `plan.html` |

Add one row per source→doc pair. When any HTML doc is regenerated, also update the
`index.html` card timestamp.

Run `html-artifacts audit` to check freshness of all listed docs.
