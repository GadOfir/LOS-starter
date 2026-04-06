---
# LOS:managed — this file is replaced by /update-los
name: evolve
description: >
  Runs a full LOS system review, health check, and improvement suggestions.
  Triggers on "review", "status", "health check", "evolve", or /evolve.
metadata:
  phase: review
  produces: System health report + automated fixes
  depends-on: []
---

# Evolve

## Part 1: Review
1. List projects in `memory/projects/` — status, task completion
2. Flag stale projects (no updates 14+ days)
3. Scan `memory/knowledge/` for stale or relevant context
4. Summarize `logs/` from last 7 days
5. Show `.tasks/` active task if exists

## Part 2: Health Check (auto-fix safe issues)
6. `.tasks/index.md` out of sync with `active/` and `done/` → rebuild
7. Each project has README.md, tasks.md, decisions.md → flag missing
8. Orphan files in `memory/` → flag (legacy dirs like areas/, entities/, decisions/ → migrate to knowledge/)
9. Logs gap > 3 days → note as "dark period"
10. Knowledge files stale > 30 days → suggest refresh
11. `# State` in `memory/identity.md` out of sync with filesystem → fix it

## Part 3: Evolution
11. Scan logs for repeated actions that aren't automated → suggest skills
12. Check `.tasks/knowledge.md` for recurring failures → suggest fixes
13. If `/create-skill` is available, suggest using it for new skills

## Output format

```
── LOS Status ─────────────────────────────────
Projects: [N active] [N stale]
  - name: status [X/Y tasks]

This week: [log summary]
Active task: [if any]

Health: [N issues found, N auto-fixed]
  ⚠ [issues needing attention]

Suggestions:
  1. [actionable thing]
──────────────────────────────────────────────
```

After output, update `# State` in `memory/identity.md`: set `Last evolve:` to today's date.

Log: `[evolve] Health check — {N} issues, {N} auto-fixed`
