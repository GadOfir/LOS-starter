# Knowledge

## task-001 · 2026-03-29 · PASS
**Goal:** Adopt BMAD patterns into LOS-starter — onboarding gate, State tracking, enriched skill metadata, hardened loop autonomy
**Learned:**
- SKILL.md custom metadata goes under `metadata:` key, not top-level frontmatter
- Inline onboarding in CLAUDE.md via conditional section beats a separate skill
- Shared State section in CLAUDE.md is the minimal BMAD config-as-code equivalent
- Autonomy wording belongs in CLAUDE.md (always loaded), not just task-system skill
**Watch out for:**
- Skills table can drift from actual .claude/skills/ dirs — /evolve catches this
- `onboarded: false` is a markdown string, not a programmatic gate — fragile if hand-edited
---
