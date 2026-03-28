---
id: task-005
title: Make CLAUDE.md routing self-assembling from skill metadata
status: PASS
phase: learn
mode: loop
target_repo:
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-29
---

# Task: Make CLAUDE.md routing self-assembling from skill metadata

## Context
- goal: Replace hardcoded Skills table and Routing in CLAUDE.md with "scan .claude/skills/" instructions so custom skills auto-appear and /update-los doesn't wipe them
- constraints: Keep it simple — no CSV manifests, no build steps. Just "scan the directory" instructions in CLAUDE.md. Also ensure memory/identity.md is loaded via CLAUDE.md directive.

## Analysis

### Problem
/update-los replaces CLAUDE.md entirely (it's a managed file). But CLAUDE.md hardcodes the Skills table and Routing section with specific skill names. When a user has custom skills (like design-html in LOS), those entries get wiped on update. The user then has to manually re-add them.

This is the exact problem BMAD's `.customize.yaml` pattern solves, but we chose a simpler path. The simpler fix: don't hardcode skills at all.

### Current state
- CLAUDE.md has a markdown table listing 6 skills by name
- Routing section has 6 hardcoded trigger→skill mappings
- Each SKILL.md already has all the metadata needed in frontmatter: name, description (contains triggers), metadata.phase, metadata.produces, metadata.depends-on

### Solution
Replace the hardcoded table and routing with a directive:

```markdown
## Skills

> At session start, scan `.claude/skills/*/SKILL.md` and build the skills
> table from each skill's frontmatter (name, description, metadata).

## Routing

> Route user intent by matching against each skill's description/triggers
> in its SKILL.md frontmatter. If unclear, ask what I'm trying to accomplish.
```

This means:
- User-created skills auto-appear without touching CLAUDE.md
- /update-los safely replaces CLAUDE.md — no custom entries to lose
- The "custom entries need manual re-add" warning in update-los becomes unnecessary
- Skills are the source of truth for their own routing

### Identity loading
CLAUDE.md line 26 already says "Always read `memory/identity.md` at session start" — sufficient, no change needed.

### Why this is safe
Claude Code already scans `.claude/skills/*/SKILL.md` and loads descriptions into context (visible in system-reminder). The hardcoded table is a redundant duplicate that goes stale. We're deleting duplication, not adding a feature.

### Architecture

Files to change:
1. `CLAUDE.md` — replace Skills table + Routing with scan directives (2 section edits)
2. `.claude/skills/update-los/SKILL.md` — remove "custom entries need manual re-add" warning

## Plan

1. Replace CLAUDE.md Skills table with directive: scan `.claude/skills/*/SKILL.md`
2. Replace CLAUDE.md Routing section with directive: match intent against skill descriptions
3. Remove "custom entries need manual re-add" warning from update-los migration
4. Verify: no hardcoded skill names in CLAUDE.md Skills/Routing sections

**Done =** CLAUDE.md has no hardcoded skill names, custom skills auto-route, update-los has no re-add warning

## Work
- Replaced CLAUDE.md Skills table (6 hardcoded rows) with scan directive: "Skills are defined in `.claude/skills/*/SKILL.md`"
- Replaced CLAUDE.md Routing section (7 hardcoded mappings) with scan directive: "match against skill description field"
- Removed "custom entries need manual re-add" warning from update-los migration step 5
- Removed hardcoded managed files table from update-los — replaced with description of LOS:managed marker pattern

## Verify
2026-03-29 · attempt 1
- CLAUDE.md grep for hardcoded skill names (task-system, build-project, etc.): 0 matches in Skills/Routing — PASS
- Onboarding references to /build-project and /task-system: present (correct — action instructions, not routing) — PASS
- update-los has no "manual re-add" warning: PASS
- update-los has no hardcoded file table: PASS
- result: PASS

## Fix

## Learn
**Insights:**
- Claude Code already scans skill descriptions into context — hardcoded tables are pure duplication
- Removing duplication is the simplest fix, not adding scan logic

**Watch out for:**
- Onboarding steps still name specific skills (/build-project, /task-system) — these are action instructions, not routing, so they're correct to keep
