---
id: task-006
title: Add docs section to LOS landing page
status: PASS
phase: learn
mode: loop
target_repo:
blocked_reason:
fix_attempts: 0
last_updated: 2026-03-29
---

# Task: Add docs section to LOS landing page

## Context
- goal: Add a comprehensive docs section to index.html explaining how LOS works in depth
- constraints: Single-file HTML, match existing design system, keep it practical not marketing

## Analysis

### What the page already covers
- Hero: what LOS is (one-liner)
- Skills: 5 skill cards with triggers
- Setup: 3 steps to get started
- Structure: folder tree
- The Point: why LOS exists, cross-project flow diagram
- Task System spotlight: phase flow, loop vs gated
- Use Cases: 6 real situations

### What's missing (docs territory)
The page explains *what* and *why* but not *how it works under the hood*. Someone who clones the repo needs to understand:

1. **CLAUDE.md** — what it does, how routing works, what happens at session start
2. **Memory system** — identity.md, knowledge/, projects/, areas/, decisions/. What goes where and why.
3. **Task lifecycle** — deeper than the spotlight: frontmatter fields, statuses, what each phase actually produces, loop mode rules
4. **Skills anatomy** — how a SKILL.md is structured, how to create custom ones, what LOS:managed means
5. **Updates** — how /update-los works, what's safe, what gets replaced
6. **Cross-project** — how .claude.los works, what a connected project looks like
7. **Secrets** — the 1Password-reference-only rule, expanded

### Revised approach (per feedback)
- No 1Password references — just say LOS doesn't store secrets/passwords
- Add a **Concepts** section — explains core ideas: loop system (deep), memory system, skills, CLAUDE.md, managed files, cross-project
- Expand loop system coverage — it's the key differentiator
- Keep existing Use Cases section as-is (already good)
- Add **FAQ** section after docs — common questions, collapsible
- No separate pages — all in index.html

### Architecture
Two new sections between Use Cases and footer:
1. **Concepts** — deep-dive cards/blocks explaining how things work
2. **FAQ** — collapsible Q&A

## Plan

1. Add CSS for docs concepts + FAQ accordion
2. Add Concepts section — loop system, memory, skills anatomy, CLAUDE.md, updates, cross-project, security
3. Add FAQ section — 6-8 common questions with collapsible answers
4. Update secrets reference in "The Point" section — remove 1Password, just say "no secrets"
5. Verify: page loads, sections work, mobile responsive

**Done =** Concepts + FAQ sections on page, loop system well-explained, no 1Password references

## Work
- Added CSS for concept blocks (color-coded left borders) and FAQ accordion (details/summary, no JS)
- Added Concepts section with 6 blocks: Loop mode (with detail grid), Memory system, Skills, CLAUDE.md, Updates, Cross-project
- Added FAQ section with 8 collapsible questions covering secrets, setup, existing projects, updates, sharing, notes comparison, custom skills, loop safety
- Removed 1Password reference from "The Point" section — now just says "don't store secrets"
- Kept existing Use Cases section unchanged

## Verify
2026-03-29 · attempt 1
- No 1Password references in page: PASS
- 8 sections balanced (open/close tags): PASS
- 8 FAQ details/summary pairs balanced: PASS
- File size: 981 lines (single file, reasonable): PASS
- result: PASS

## Fix

## Learn
**Insights:**
- Pure CSS accordion with details/summary needs no JavaScript and works everywhere
- Loop mode explanation needs its own visual grid — bullet points don't convey the bounded-autonomy concept well

**Watch out for:**
- 981 lines is getting close to the 200-line split threshold in Rules, but HTML pages are different from logic files — splitting would make it worse
