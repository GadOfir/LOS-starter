---
id: task-007
title: Quick fixes — portability wording, docs copy, nav link, migration guard note
status: PASS
phase: learn
mode: loop
target_repo:
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-29
---

# Task: Quick fixes — portability wording, docs copy, nav link, migration guard note

## Context
- goal: Fix small gaps: task-system portability wording, .claude.los as optional in docs, "not just for devs" messaging, GitHub repo link in nav
- constraints: Minimal edits. No structural changes.

## Plan

1. Task-system SKILL.md — wrap memory/identity.md references in "if exists" language so it's clean in non-LOS repos
2. index.html — soften .claude.los to optional (tell Claude or drop file)
3. index.html — add 1-2 non-dev examples to messaging (hero-sub, use cases)
4. index.html — add GitHub repo link to nav bar
5. Verify all changes

**Done =** task-system portable, docs accurate, nav has repo link

## Work
- Task-system: wrapped memory/identity.md references in "if exists" (lines 191, 476)
- Docs: softened .claude.los to optional in 4 places (tree, The Point, cross-project concept, FAQ)
- Hero: broadened to "dev projects, research, hobbies, life goals"
- Use cases: changed "Learning something new" to "Learning anything" (sourdough + Terraform), added "Tracking non-code projects" case
- Nav: added GitHub repo link

## Verify
2026-03-29 · attempt 1
- Section tags balanced (8/8): PASS
- Task-system has "if exists" + "Skip if not in LOS repo" on both identity refs: PASS
- .claude.los mentioned 4 times, all framed as optional: PASS
- GitHub link in nav: PASS
- result: PASS

## Fix

## Learn
**Insights:**
- .claude.los was never required — it's just a convenience file. Docs were making it sound mandatory.
- Task-system portability only needed two line edits — the skill was already self-contained.
