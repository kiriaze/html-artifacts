# html-artifacts — Gemini CLI / GEMINI.md snippet

Add the following to your project's `GEMINI.md` (read by Gemini CLI at session start).
Gemini CLI loads skills via `activate_skill` — the stale-doc reminder is implemented as
an instruction rule, not a process hook.

---

## html-artifacts skill

When the `html-artifacts` skill is available, invoke it (via `activate_skill`) before
producing any plan, analysis, architecture overview, competitor analysis, or report.

**Stale-doc reminder rule:** After any write to `docs/plans/*.md` or `docs/architecture.md`,
print before your next response:

> ⚠ html-artifacts: source doc changed — run `html-artifacts refresh` to update `docs/html/`

**HTML vs Markdown decision tree:**

```
Plan, analysis, report, diagram, or explainer?       → HTML  (invoke html-artifacts skill)
Reviewed as a git diff (HANDOFF.md, AGENT_TASKS.md)? → Markdown
Embedded in GitHub (README, PR body, issue)?         → Markdown
Commit message, inline comment, or chat reply?       → plain text
Output < 10 lines?                                   → Markdown or plain
```

Interactive HTML docs live in `docs/html/`. See `docs/html/README.md` for the full index.

---

## Gemini CLI tool mapping

When following `SKILL.md` instructions that reference Claude Code tool names, use these equivalents:

| SKILL.md reference | Gemini CLI equivalent |
|---|---|
| `Write` tool | `write_file` |
| `Read` tool | `read_file` |
| `Bash` tool | `run_shell_command` |
| `Skill` tool | `activate_skill` |
