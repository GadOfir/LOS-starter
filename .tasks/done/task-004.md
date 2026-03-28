---
id: task-004
title: Design safe LOS update mechanism with memory preservation + audit trail
status: PASS
phase: learn
mode: loop
target_repo:
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-29
---

# Task: Design safe LOS update mechanism with memory preservation + audit trail

## Context
- goal: Figure out how to safely update an old LOS installation (skills, CLAUDE.md structure, task-system) without losing memory/, projects, knowledge, or user customizations. Add an audit mechanism so users know what changed.
- constraints: LOS-starter is the upstream template. LOS (personal) is a downstream instance. Memory system must survive updates intact. Look at how BMAD handles updates for inspiration.

## Analysis

### The problem
LOS-starter is a template repo. Users fork/clone it into their own `LOS` instance and customize it — adding memory, projects, knowledge, identity, tasks. When LOS-starter gets updated (new skills, CLAUDE.md structure changes, task-system improvements), there's no safe way to pull those updates into an existing LOS instance without risking:
- Overwriting `memory/`, `.tasks/`, `logs/`
- Losing Identity/State in CLAUDE.md
- Breaking customized skills

### How BMAD solves this
BMAD uses a **two-tier file system**:
- **Base files** (agents, skills) — overwritten on every update
- **`.customize.yaml` files** — user modifications that survive updates, merged back in after update

Key patterns:
1. `npx bmad-method install --action quick-update` — only prompts for new fields, preserves existing config
2. `.customize.yaml` per agent — deep-merged onto base files (some fields replace, some append)
3. Semantic versioning + CHANGELOG.md for audit
4. CLI handles all file operations — users never manually merge

### What LOS needs (adapted from BMAD)
LOS is simpler than BMAD — no CLI, no YAML compilation. But the core idea applies:

**Protected (never touched by updates):**
- `memory/` — all user data
- `.tasks/` — task history, knowledge, index
- `logs/` — session logs
- `CLAUDE.md` sections: Identity, State (user-filled data)
- Any user-created skills in `.claude/skills/`

**Updatable (replaced by updates):**
- `.claude/skills/*/SKILL.md` — core skill definitions (task-system, learn, build-project, evolve, create-skill)
- `CLAUDE.md` sections: Onboarding flow, Skills table, Routing, Autonomy, Rules, Self-Healing (structural sections)

**The hard part:** CLAUDE.md is one file with both protected and updatable sections. Options:
1. **Split CLAUDE.md** — `CLAUDE.md` (updatable structure) + `IDENTITY.md` (protected user data) → cleanest but breaking change
2. **Section markers** — `<!-- LOS:protected -->` / `<!-- LOS:updatable -->` markers, update script respects them
3. **Merge strategy** — like BMAD's customize pattern, keep user sections and replace structural sections

Option 1 is cleanest. BMAD went through a similar split (`.bmad` → `_bmad/`).

### Architecture

**Version tracking:**
- Add `VERSION` file to LOS-starter root (semver, e.g., `0.2.0`)
- Add `los_version` field to CLAUDE.md frontmatter or State section
- CHANGELOG.md in LOS-starter (not in user instances)

**Update mechanism (no CLI needed — Claude IS the CLI):**
- New skill: `/update-los` — Claude reads VERSION from upstream (GitHub raw), compares to local, shows diff, applies changes
- Or simpler: a `UPDATE.md` guide that tells Claude what to do (works without a dedicated skill)

**Audit trail:**
- `logs/{date}.md` entry: `[update] LOS updated from v0.1.0 to v0.2.0 — changes: [list]`
- Before updating, snapshot current skill hashes for rollback reference

### Decision: keep CLAUDE.md as single file
User prefers one CLAUDE.md. Instead of splitting, move Identity + State data into `memory/identity.md` (protected). CLAUDE.md stays fully updatable — it just reads identity from memory on load.

This is better because:
- CLAUDE.md is 100% updatable (no protected sections to dance around)
- Identity lives in `memory/` which is already in the "never touch" list
- Follows the existing memory system pattern

### CLAUDE.md update strategy
CLAUDE.md becomes a template with a directive: `> Read memory/identity.md for user identity and state.`
On update, the entire CLAUDE.md gets replaced. Identity survives because it's in memory/.

## Plan

1. Create `memory/identity.md` — move Identity + State data there (name, role, goal, projects, active task, last session, last evolve)
2. Update CLAUDE.md — replace `## Identity` and `## State` with a single directive pointing to `memory/identity.md`. Mark `onboarded` status there too.
3. Add `VERSION` file to LOS-starter root — start at `0.2.0`
4. Add `<!-- LOS:managed -->` comment at top of CLAUDE.md and each core skill SKILL.md so update skill knows what it can replace
5. Create `/update-los` skill — reads upstream VERSION from GitHub, compares to local, shows diff of managed files, applies changes while skipping `memory/`, `.tasks/`, `logs/`
6. Add `CHANGELOG.md` to LOS-starter with current state as v0.2.0 baseline
7. Update CLAUDE.md routing + skills table with new update-los skill
8. Verify: identity.md has correct data, CLAUDE.md loads it, managed markers present, update-los skill exists

**Done =** CLAUDE.md is fully updatable, identity lives in memory/, VERSION + CHANGELOG exist, update-los skill works

## Work
- Created `memory/identity.md` with Identity + State fields (migrated current values)
- Rewrote CLAUDE.md: removed Identity/State sections, added directive to read `memory/identity.md`, added `LOS:managed` marker
- Updated onboarding flow to write to `memory/identity.md` instead of CLAUDE.md
- Updated all skills (task-system, evolve, build-project) to reference `memory/identity.md` for State
- Updated Self-Healing section to reference `memory/identity.md`
- Created `VERSION` file (0.2.0)
- Added `# LOS:managed` YAML comment to all 6 core skill SKILL.md files
- Created `/update-los` skill with full update flow: version check → fetch → diff preview → apply → audit log
- Created `CHANGELOG.md` with v0.1.0 and v0.2.0 entries
- Added update-los to CLAUDE.md skills table and routing

## Verify
2026-03-29 · attempt 1
- memory/identity.md exists with correct Identity + State fields: PASS
- CLAUDE.md has LOS:managed marker and no Identity/State sections: PASS
- CLAUDE.md onboarding references memory/identity.md: PASS
- All 7 managed files have LOS:managed marker (CLAUDE.md + 6 skills): PASS
- No stale "State in CLAUDE.md" references in skills: PASS
- VERSION file exists with 0.2.0: PASS
- update-los skill exists with complete flow: PASS
- CHANGELOG.md exists with v0.1.0 and v0.2.0: PASS
- Skills table and routing include update-los: PASS
- result: PASS

## Fix

## Learn
**Insights:**
- BMAD's `.customize.yaml` pattern (base files + overlay) is powerful but overkill for LOS — simpler approach: move user data to memory/, make everything else replaceable
- The key design insight: CLAUDE.md should be a template with zero user state, so it can be fully replaced on update
- `LOS:managed` markers make the update boundary explicit — any file without it is user territory
- Claude itself can be the update CLI — no need for npm/npx tooling when the skill can fetch from GitHub raw URLs

**Watch out for:**
- CHANGELOG.md mentions "breaking" changes for v0.2.0 — existing LOS instances need a one-time migration of identity data
- The task-004 file itself has `LOS:managed` in the grep results because it discusses the marker — not actually a managed file
