---
id: task-001
title: Adopt BMAD patterns for LOS-starter
status: PASS
phase: learn
mode: loop
target_repo: c:/Dev/LOS-starter
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-28
---

# Task: Adopt BMAD patterns for LOS-starter

## Context
- goal: Take the best patterns from BMAD method and integrate them into LOS-starter without breaking what works
- constraints: LOS-starter is a public repo people clone as a cold-start template. Changes must be incremental, not a rewrite. Keep the simplicity that makes LOS attractive.
- branch: bmad-patterns (new branch in LOS-starter repo)

## Analysis

### What problem are we solving?
LOS-starter's CLAUDE.md is a static routing table. It tells Claude "when you hear X, run Y" but:
- It doesn't adapt to what's already been done (no completion detection)
- It doesn't carry project-specific context beyond what's in memory/
- Cold start dumps the user into a blank system with no guided onboarding
- Skills are independent — no orchestration layer ties them into a coherent lifecycle

BMAD solves these by treating the orchestration file as a living system, not a static config.

### What BMAD does well (research findings)
1. **CSV-driven routing** — a manifest catalogs every skill with phase, dependencies, completion patterns. The help skill reads filesystem state to know what's done.
2. **Agent personas** — YAML identity files separate from SKILL.md behavior files. Agents don't do work; they route to workflows while maintaining character.
3. **Step-file architecture** — complex workflows split into micro-files loaded one at a time. Prevents LLM from "looking ahead."
4. **Config-as-code cold start** — `bmad-init` walks users through questions, creates config + dirs declaratively.
5. **Frontmatter state tracking** — `stepsCompleted` arrays in output docs enable resume/continuation.
6. **Explicit failure metrics** — every step tells the LLM what constitutes system failure.

### What we should NOT adopt (now)
- Agent personas (Mary, Winston) — overkill for a personal system
- npm package / installer — LOS is a git repo you clone
- Step-file micro-architecture — our skills are already small
- Party mode / multi-agent — irrelevant for personal use

### What we defer to later (if skill count grows past ~8)
- CSV routing manifest — when CLAUDE.md routing gets unwieldy, extract to a manifest file that a /help skill reads. Not needed yet at 5 skills.

### Architecture

**Principle: keep skill count LOW.** Every new skill is maintenance debt. Prefer enriching existing skills over adding new ones. The cold-start skill is special — it runs once and removes itself.

**What we SHOULD adopt, adapted for LOS:**

1. **Self-deleting cold-start skill** — When someone clones LOS-starter and runs Claude for the first time, CLAUDE.md detects empty state (no `## State` section, or `onboarded: false`) and triggers cold-start behavior INLINE — no separate skill file. It asks 3 setup questions, writes a personalized CLAUDE.md, scaffolds the first project, then flips `onboarded: true`. The cold-start instructions live in CLAUDE.md itself behind a conditional: "If `onboarded: false` → run onboarding. Otherwise → normal routing." This means zero extra skills to maintain.

2. **Enriched CLAUDE.md as the brain** — CLAUDE.md becomes the single orchestration file:
   - `## Identity` — user name, role, goals (written by cold-start)
   - `## Skills` — manifest with trigger phrases, what each produces, phase (setup/daily/review)
   - `## Routing` — same as today but informed by Skills manifest
   - `## State` — updated by skills as they run (projects list, last evolve date, etc.)
   - `## Rules` — same as today

3. **End-of-onboarding pattern** — After cold-start completes, CLAUDE.md should:
   - Suggest the user's logical next action based on what they said their goal is
   - Pre-populate `## State` with the first project
   - Write a `logs/` entry so the system has history from day one
   - Print a "you're set up — here's what to do next" message

4. **Skill frontmatter enrichment** — Add `phase`, `produces`, `depends-on` to each skill YAML. This is metadata for CLAUDE.md's Skills manifest, not a new routing system.

5. **Hardened loop behavior** — Add to CLAUDE.md and task-system:
   ```
   ## Autonomy
   When in loop mode: NEVER STOP to ask "should I continue?" or
   "is this a good stopping point?". Execute the full plan. If stuck,
   think harder — re-read files, try alternatives, combine approaches.
   Only stop on: STUCK (2 failed fixes), BLOCKED (missing external dep),
   or task PASS. The loop runs until completion or failure, period.
   ```

## Plan

### Revision 2026-03-28 — hardened, refined per feedback

**Target repo: c:/Dev/LOS-starter (NOT c:/Dev/LOS)**

1. Create branch `bmad-patterns` in LOS-starter repo
2. Rewrite CLAUDE.md as a dynamic orchestration file:
   - Add `onboarded: false` marker at top
   - Add `## Onboarding` section with inline cold-start flow (3 questions → writes Identity + State + first project)
   - Add `## Identity` (blank, filled by onboarding)
   - Add `## Skills` manifest (name, triggers, produces, phase, depends-on for each skill)
   - Add `## State` section (blank, updated by skills)
   - Add `## Autonomy` section with hardened loop rules
   - Keep `## Routing` but make it reference Skills manifest
   - Keep `## Rules` and `## Self-Healing`
   - Add conditional: "If onboarded: false → run Onboarding. Else → normal Routing."
3. Enrich each existing skill's SKILL.md frontmatter with `phase`, `produces`, `depends-on`
4. Update task-system skill to respect `## Autonomy` rules and update `## State` on task close
5. Update evolve skill to read/update `## State` instead of scanning filesystem blind
6. Update build-project skill to append new projects to `## State`
7. Test the cold-start flow: simulate fresh clone (empty Identity + State), verify onboarding writes correct CLAUDE.md
8. Test post-onboarding: verify routing still works, skills still trigger, State gets updated
9. Update index.html landing page to mention the guided cold-start experience
10. Final verify: read every changed file, confirm no broken references, no orphan skills

**Done =** Fresh clone → Claude reads CLAUDE.md → detects onboarded:false → asks 3 questions → writes personalized Identity+State → suggests first action → all skills work with enriched metadata

**Loop rules for this task:**
- Steps 2-6 are the core build. Do them sequentially, each modifying files in LOS-starter.
- Steps 7-8 are verify. Read files and trace logic, don't need to actually clone.
- Step 9 is a quick HTML edit.
- Step 10 is final sweep. If anything is broken, fix it inline — don't stop to ask.
- If a file edit breaks something downstream, fix it immediately before moving on.
- Do NOT stop between steps to ask for approval. Execute the full plan.

## Work
- Step 1: Branch bmad-patterns already existed (current branch)
- Step 2: Rewrote CLAUDE.md — added Onboarding gate, Identity, Skills manifest table, Routing, State, Autonomy (NEVER STOP wording), Rules, Self-Healing with State sync
- Step 3: Enriched all 5 skill frontmatters with `metadata:` block containing phase/produces/depends-on (used `metadata` key to satisfy SKILL.md schema)
- Step 4: Updated task-system — added State update on session start (step 7) and on close (step 4)
- Step 5: Updated evolve — added State sync check in health check (step 11) and Last evolve date update after output
- Step 6: Updated build-project — added State append for new projects (step 6)
- Step 9: Updated index.html — step 2 now mentions guided onboarding, structure section shows CLAUDE.md as orchestration brain
- Steps 7-8: Verified cold-start flow (Identity empty → onboarding → fills Identity/State → creates project → suggests next action) and post-onboarding flow (routing works, all skills trigger correctly, State updated by 3 skills)
- Step 10: Final sweep — 7 files changed, 5 skill dirs match 5 table rows, no orphan references

## Verify
2026-03-29 · attempt 1
- CLAUDE.md has onboarding gate with `onboarded: false`: PASS
- Identity section is blank (template-ready): PASS
- Skills manifest table has all 5 skills with correct triggers/produces/phase: PASS
- Routing section references all 5 skills: PASS
- State section present with 4 trackable fields: PASS
- Autonomy section has NEVER STOP wording: PASS
- All 5 SKILL.md files have metadata block with phase/produces/depends-on: PASS
- task-system updates State on session start + close: PASS
- evolve updates Last evolve + syncs State in health check: PASS
- build-project appends to State Projects list: PASS
- index.html mentions guided onboarding in setup steps: PASS
- 5 skill directories = 5 table rows (no orphans): PASS
- result: PASS
- reason: All done criteria met — cold-start flow is complete, skills enriched, State wired up

## Learn

**Insights:**
- SKILL.md frontmatter only supports specific keys — custom metadata must go under `metadata:` object, not as top-level keys
- Inline onboarding in CLAUDE.md (conditional section) is far simpler than a separate cold-start skill — one less file to maintain, and the gate logic is obvious
- The "State" section in CLAUDE.md as a shared scratchpad between skills is the minimal viable version of BMAD's config-as-code — it works without CSV manifests or agent personas
- Strong autonomy wording ("NEVER STOP", "think harder") needs to be in CLAUDE.md (always loaded) not just in the task-system skill (only loaded when invoked)

**Watch out for:**
- If the Skills table in CLAUDE.md gets out of sync with actual .claude/skills/ directories, onboarding and routing will give wrong info — evolve's Self-Healing check covers this but only if you remember to run /evolve
- The `onboarded: false` marker is a plain string in markdown — Claude has to pattern-match it, there's no programmatic gate. If someone edits CLAUDE.md carelessly they could break the conditional
