# Knowledge

## task-006 · 2026-03-29 · PASS
**Goal:** Add Concepts + FAQ docs sections to landing page
**Learned:**
- details/summary CSS accordion needs no JS — works everywhere
- Loop mode needs visual grid to convey bounded autonomy, not just bullets
**Watch out for:**
- index.html at 981 lines — don't split HTML pages like logic files
---

## task-005 · 2026-03-29 · PASS
**Goal:** Make CLAUDE.md routing self-assembling — remove hardcoded skill lists
**Learned:**
- Claude Code already scans skill SKILL.md descriptions into context — hardcoded tables are duplication
- Removing duplication is simpler and more maintainable than adding scan logic
**Watch out for:**
- Onboarding steps reference specific skills by name — those are action instructions, not routing, so they stay
---

## task-002 · 2026-03-29 · PASS
**Goal:** Sync cross-repo tasks + back-port BMAD patterns to LOS
**Learned:**
- Cross-repo sync requires checking both directions — features can exist in either repo
- design-html is a LOS-only custom skill, not part of the starter template
**Watch out for:**
- Always run verify after build, even if work log looks complete — task-002 was left dangling
---

## task-004 · 2026-03-29 · PASS
**Goal:** Design safe LOS update mechanism — preserve memory, add version tracking and audit
**Learned:**
- Move user data to memory/ so CLAUDE.md + skills are 100% replaceable on update
- `LOS:managed` markers make update boundary explicit — files without it are user territory
- Claude itself acts as the update CLI via /update-los skill (fetch GitHub raw → diff → apply)
- BMAD's customize.yaml pattern is overkill for LOS — simpler file separation works
**Watch out for:**
- Existing LOS instances need one-time migration of Identity/State to memory/identity.md
- CHANGELOG "breaking" section should include migration steps for users
---

## task-003 · 2026-03-29 · PASS
**Goal:** Add autonomy reinforcement to task-system loop mode + target_repo guard in build phase
**Learned:**
- Skill prompts are the primary context during execution — CLAUDE.md rules need reinforcement inside the skill
- Cross-repo handoff needs guards at the action point (Build), not just a standalone reference section
- Back-port between repos can happen from either side — check before assuming it's needed
**Watch out for:**
- Two copies of task-system SKILL.md (LOS + LOS-starter) can drift — always diff after edits
---

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
