---
id: task-002
title: Sync cross-repo tasks + back-port BMAD patterns to LOS
status: IN_PROGRESS
phase: build
mode: loop
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-29
---

# Task: Sync cross-repo tasks + back-port BMAD patterns to LOS

## Context
- goal: Fix gaps from task-001 — changes were applied to one repo but not the other
- constraints: Work on bmad-patterns branch in LOS-starter, directly in LOS for LOS changes
- LOS-starter branch: bmad-patterns (on top of 365e891)

## Analysis

Task-001 applied BMAD patterns to LOS-starter but some features exist in one repo and not the other:

**LOS-starter missing:**
- `## Cross-repo tasks` section in task-system SKILL.md
- `target_repo:` field in task file format template

**LOS missing:**
- `metadata:` blocks on all skills (phase, produces, depends-on)
- `## State` update steps in task-system (session start + close)
- CLAUDE.md upgrade: Onboarding, Identity, Skills table, State, Autonomy sections

### Architecture
- LOS-starter gets the generalized cross-repo rules (any LOS-starter user might plan in LOS and build elsewhere)
- LOS gets back-ported metadata + CLAUDE.md upgrade adapted for Gad specifically (not the blank template)
- Both task-system SKILLs should be functionally identical after this

## Plan

1. Add `## Cross-repo tasks` section + `target_repo:` field to LOS-starter's task-system SKILL.md
2. Add `metadata:` blocks to all LOS skills (build-project, create-skill, evolve, learn, task-system)
3. Add `## State` update steps to LOS's task-system (session start step 7, close step 4)
4. Upgrade LOS CLAUDE.md with BMAD patterns (Identity, Skills table, State, Autonomy) — personalized for Gad
5. Verify: diff both task-system SKILLs — should be structurally identical
6. Verify: all skills in both repos have metadata blocks
7. Verify: LOS CLAUDE.md has all sections that starter has (adapted, not copied)

**Done =** Both repos have all features, no gaps, task-systems match structurally

## Work
- step 1: Added `## Cross-repo tasks` section + `target_repo:` field to LOS-starter task-system SKILL.md
- step 2: Added `metadata:` blocks to all 6 LOS skills (build-project, create-skill, design-html, evolve, learn, task-system)
- step 3: Added `## State` update steps to LOS task-system (session start step 7, close step 4)
- step 4: Rewrote LOS CLAUDE.md with BMAD patterns — Identity (Gad), Skills table (6 skills incl design-html), State (pre-populated), Autonomy, onboarded:true
- step 5 verify: Both task-system SKILLs have identical section headers
- step 6 verify: All skills in both repos have metadata blocks (fixed design-html which was missing)
- step 7 verify: LOS CLAUDE.md has all sections except Onboarding (correct — already onboarded)

## Verify

## Fix

## Learn
