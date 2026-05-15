# html-artifacts — Codex / AGENTS.md hook snippet

Codex has no settings hook system. Add the following rule to your project's `AGENTS.md`
(or `CLAUDE.md` / `GEMINI.md` — whichever your agent reads at session start).

---

## html-artifacts: stale-doc reminder

After writing or editing any file under `docs/plans/` or any file matching `docs/architecture.md`,
print the following message before continuing:

> ⚠ html-artifacts: source doc changed — run `html-artifacts refresh` to update `docs/html/`

---

## html-artifacts: when to use HTML vs Markdown

Apply this decision tree before producing any documentation, plan, or report output:

```
Plan, analysis, report, diagram, or explainer?       → HTML  (use html-artifacts skill)
Reviewed as a git diff (HANDOFF.md, AGENT_TASKS.md)? → Markdown
Embedded in GitHub (README, PR body, issue)?         → Markdown
Commit message, inline comment, or chat reply?       → plain text
Output < 10 lines?                                   → Markdown or plain
```

Interactive HTML docs live in `docs/html/`. See `docs/html/README.md` for the full index.
