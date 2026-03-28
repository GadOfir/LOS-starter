---
id: task-003
title: Add autonomy prompt to task-system + harden cross-repo handoff
status: IN_PROGRESS
phase: verify
mode: loop
target_repo:
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-29
---

# Task: Add autonomy prompt to task-system + harden cross-repo handoff

## Context
- goal: Fix two gaps from task-002 — autonomy rules missing from task-system SKILL.md, and cross-repo handoff too easy to skip
- constraints: Changes apply to BOTH repos. Plan here in LOS, build in LOS-starter (bmad-patterns branch), then back-port to LOS.

## Analysis

### Problem 1: Autonomy prompt placement
The "NEVER STOP" autonomy rules live in CLAUDE.md (both repos). But during loop execution, Claude reads the **task-system SKILL.md** as its primary prompt. CLAUDE.md is background context — if the skill prompt doesn't reinforce autonomy, Claude will still pause mid-loop to ask for approval.

Fix: Add a compact autonomy reminder to the Loop mode section of task-system SKILL.md. Not a full copy of the CLAUDE.md section — just a reinforcement that references it.

### Problem 2: Cross-repo handoff too easy to skip
The `## Cross-repo tasks` section exists but:
- It's a standalone section between "Loop mode" and "Task file format" — easy to miss during build phase
- The Build phase section itself has no mention of target_repo
- In loop mode, Claude's build logic reads `## Plan` and `## Work` — it never re-reads the cross-repo section

Fix: Add a **pre-build check** directly in the Build phase section: "Before executing: if `target_repo` is set → STOP, follow cross-repo handoff rules."

### Architecture

Files to change (both repos):
1. `.claude/skills/task-system/SKILL.md` — two edits:
   - Loop mode section: add autonomy reinforcement
   - Build phase section: add target_repo pre-check

That's it. Two surgical edits, same file, both repos.

## Plan

1. Edit LOS-starter task-system SKILL.md — add autonomy reinforcement to Loop mode section
2. Edit LOS-starter task-system SKILL.md — add target_repo pre-check to Build phase section
3. Back-port both edits to LOS task-system SKILL.md
4. Verify: grep both files for "NEVER STOP" and "target_repo" — both should appear in Loop and Build sections
5. Verify: diff section headers — still identical between repos

**Done =** Both task-system SKILLs have autonomy in Loop section + target_repo guard in Build section

## Work
- Picked up task in LOS-starter (handoff from LOS planning repo)
- Edit 1: Added "Autonomy reminder (loop mode only)" block to Loop mode section (line 88) — reinforces CLAUDE.md autonomy rules directly in the skill prompt
- Edit 2: Added "Pre-check: if target_repo is set → STOP" to Build phase section (line 370) — prevents build from running in wrong repo

## Verify
2026-03-29 · attempt 1 (LOS-starter edits only, steps 1-2)
- Autonomy reminder in Loop mode section (line 88): PASS
- target_repo pre-check in Build phase section (line 370): PASS
- Both edits are positioned correctly within their sections: PASS
- result: PASS (for steps 1-2)
- remaining: Steps 3-5 (back-port to LOS repo) must be done in c:/Dev/LOS

## Fix

## Learn
