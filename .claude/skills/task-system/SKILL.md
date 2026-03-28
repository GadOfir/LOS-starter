---
name: task-system
description: >
  Manages a single-task-at-a-time project workflow inside the repo.
  Triggers on "start session", "cold start", "new task", "task status",
  "plan", "build", "verify", "fix", "close task", "compact", "what's next",
  or references to .tasks/ files and task states (IN_PROGRESS, BLOCKED, STUCK, PASS, FAIL).
  Handles full lifecycle: bootstrap → plan → build → verify → fix → learn → close.
  Supports human-gated and autonomous loop modes.
---

# Task System

A lightweight, file-based task system for Claude Code projects.
One task at a time. All output goes into the task file. Nothing gets scattered.

> **Docs tip:** For project documentation, pair this with
> [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).
> Install: `pip install mkdocs-material && mkdocs new . && mkdocs serve`

---

## Core concept: one file is the truth

Every task is a single markdown file. ALL work output — brainstorming, analysis,
architecture decisions, code plans, build logs, test results — goes into sections
of that file. Nothing gets written to separate docs, briefs, or artifacts.

If you're wearing a "hat" (analyst, architect, PM — see Plan phase), your output
still goes into the task file under the current section. The task file is the
single source of truth.

---

## Folder layout

```
.tasks/
  active/          ← only ONE task file at a time
  done/            ← archived tasks
  index.md         ← backlog + active pointer
  knowledge.md     ← accumulated learnings (newest first)
CLAUDE.md          ← project context, always loaded
```

---

## Loop mode vs gated mode

The system supports two modes. The human chooses at session start.

### Gated mode (default)
Human approves each phase before it runs. Claude proposes, human says yes/no.
Use this when you're actively working and want control.

### Loop mode
Human says "loop it" or "run through" or starts Claude Code with `/loop`.
Claude runs phases autonomously without stopping for approval, following these
rules:

**Loop auto-advance rules:**
1. After plan → if plan looks complete → auto-advance to build
2. After build → always auto-advance to verify
3. After verify PASS → auto-advance to learn → close
4. After verify FAIL → auto-advance to fix → verify (up to 2 fix attempts)
5. After 2 failed fixes → STOP. Set STUCK. Exit loop. Wait for human.

**Loop NEVER auto-advances through:**
- STUCK (always stops)
- BLOCKED (always stops)
- Plan phase when task is brand new (needs at least one human confirmation
  that the plan direction is right, then loop can take over from build onward)

**How to enter loop mode:**
- Human says: "loop it", "run through", "auto", "let it ride", "handle it"
- Or: human approves plan and adds "then loop the rest"
- System writes `mode: loop` to frontmatter and stops asking for approval

**How to exit loop mode:**
- Any STUCK or BLOCKED status
- Human interrupts with new instruction
- Task closes (PASS → learn → close completes)

---

## Task file format

```markdown
---
id: task-001
title: Short description
status: IN_PROGRESS
phase: plan
mode: gated
blocked_reason:
fix_attempts: 0
last_updated: 2025-03-24
---

# Task: Short description

## Context
- goal: what we're trying to accomplish
- constraints: any known limits

## Analysis
(brainstorming, research, domain analysis — filled during plan phase)

## Plan
(numbered steps — concrete, short, one action each)

## Work
(append-only log — one bullet per action taken during build)

## Verify
(test results — NEVER edited or deleted)

## Fix
(fix attempts — NEVER edited or deleted)

## Learn
(written once on completion)
```

### Statuses
- **IN_PROGRESS** — work is happening
- **FAIL** — verify found a problem
- **STUCK** — fix tried and verify failed again; human must intervene
- **BLOCKED** — human knows the fix but paused; `blocked_reason` required
- **PASS** — verify passed; ready for learn + close

### Phases
`plan` → `build` → `verify` → `fix` (if needed) → `learn`

---

## Session start

At the start of every conversation, before anything else:

1. Check if `.tasks/` exists. If not → **Cold Start** (below).
2. Scan `.tasks/active/` for a task file.
3. If empty + `index.md` exists → offer next backlog item.
4. If task found → read frontmatter, render briefing.
5. Re-sync `index.md` Active pointer to match what's actually in `active/`.
6. Load `knowledge.md` into context.
7. **Ask: gated or loop mode?** (unless human already specified)

### Briefing templates

**IN_PROGRESS:**
```
── Session ────────────────────────────────────
Task: [title] · phase: [phase]
Last action: [last bullet from ## Work]
Proposed: run [current phase]
→ Approve? Loop it? Or tell me what to do.
```

**FAIL:**
```
── Session ────────────────────────────────────
Task: [title] · FAIL · fix attempts: [n]
Failure: [last verify result]
(1) run fix    (2) mark BLOCKED    (3) revise plan
→ Pick one. Or "loop it" to auto-fix.
```

**STUCK:**
```
── Session ────────────────────────────────────
⚠ STUCK — nothing runs until you decide.
Task: [title] · fix attempts: [n]
(1) I fixed it manually    (2) revise plan    (3) abandon
→ Your call. (loop mode cannot override STUCK)
```

**BLOCKED:**
```
── Session ────────────────────────────────────
Task: [title] · BLOCKED
Reason: [blocked_reason]
Proposed fix: [suggestion]
(1) proceed    (2) revise plan
→ Pick one.
```

**PASS (close not yet run):**
```
── Session ────────────────────────────────────
Task: [title] · PASS ✓
→ Run close? (writes learnings, archives)
```

---

## Cold Start

Triggered when `.tasks/` doesn't exist or `active/` is empty with no `index.md`.

### Ask four questions:

```
Let's set up your task system.

1. Project name?
2. What kind of project? (code / docs / mixed)
3. Main goal? (one sentence)
4. First task to work on?
```

### Then create everything:

**`.tasks/index.md`:**
```markdown
# Active
→ task-001 · IN_PROGRESS · phase: plan · [today]

# Backlog

# Done

# Notes
Project: [name] · Type: [type] · Goal: [goal] · Started: [today]
```

**`.tasks/knowledge.md`:**
```markdown
# Knowledge
```

**`.tasks/active/task-001.md`:** (use task file format above)

**`CLAUDE.md`:**
```markdown
# [name] — Task System

## System
- Tasks: .tasks/active/ (one at a time)
- Knowledge: .tasks/knowledge.md
- Index: .tasks/index.md

## Rules
1. One file in active/ at a time
2. Human approves plan before first build (even in loop mode)
3. ALL output goes into the task file — no separate docs
4. Work, Verify, Fix are append-only
5. STUCK = full stop, human decides
6. BLOCKED needs blocked_reason
7. Close only on PASS or ABANDONED

## Project
Name: [name] · Type: [type] · Goal: [goal]
```

### Report:
```
── Setup complete ─────────────────────────────
✓ .tasks/ created with index, knowledge, first task
✓ CLAUDE.md written
Task: task-001 · phase: plan
→ Run plan? Or "loop it" to go autonomous after plan.
```

---

## Phase: Plan (BMAD-inspired, single-file output)

This is the most important phase. Instead of just writing a numbered list,
the plan phase uses three "thinking hats" in sequence. All output goes
directly into the task file — never into separate documents.

### Hat 1: Analyst → writes to `## Analysis`

Think as a business/domain analyst:
- What problem are we actually solving?
- Who benefits? What's the user story?
- What are the constraints, risks, unknowns?
- Are there competing approaches?

Write a focused analysis (3-10 bullets) into `## Analysis`.
If `knowledge.md` has relevant past learnings, reference them here.

### Hat 2: Architect → appends to `## Analysis`

Think as a technical architect. Based on the analysis above:
- What's the right technical approach?
- What components/files are involved?
- Dependencies and integration points?
- What could go wrong technically?

Append an `### Architecture` subsection inside `## Analysis`.

### Hat 3: PM → writes to `## Plan`

Think as a PM / scrum master. Based on analysis + architecture:
- Break work into numbered steps (max 8-10)
- Each step = one concrete action, not a vague goal
- Include verify criteria at the end ("done" = what exactly?)
- Order by dependency

Write to `## Plan`. Step format:
```
1. [action verb] [specific thing] — [why, if not obvious]
```

### After all three hats:

If knowledge.md has relevant entries:
```
**From knowledge:** [task-id] — [relevant insight]
```

**In gated mode:** Show the full Analysis + Plan, ask for approval.
**In loop mode on a NEW task:** STOP here. Show plan to human. After human
confirms, set `mode: loop` and auto-advance to build.
**In loop mode on a REVISED plan:** If human already approved the revision
direction, auto-advance to build.

**Sets:** phase → build, status → IN_PROGRESS

---

## Phase: Build

**Reads:** ## Plan, ## Work (avoid repeats), ## Fix (avoid failed approaches)

**Does:**
1. Execute plan steps one at a time
2. Append to `## Work` — one bullet per action: `- [action]: [what was done]`
3. Important output (config decisions, code snippets, etc.) goes inline
   in the Work bullet or as an indented sub-block — never a separate file

**Sets:** phase → verify

**Hard stop (even in loop mode):** If a plan step is wrong or impossible:
- Write `- STOPPED: [reason]` to ## Work
- Set status → FAIL, stay in build phase
- Exit loop if in loop mode, report to human

---

## Phase: Verify

**Reads:** ## Plan (success criteria), ## Work (what was done)

**Appends to `## Verify`:**
```
[date] · attempt [N]
- [check]: [result]
- result: PASS or FAIL
- reason: [one line]
```

**On PASS:** status → PASS, phase → learn
**On FAIL:** status → FAIL, phase → fix

If `fix_attempts >= 1` (fix was tried and failed again):
- status → STUCK
- If in loop mode: exit loop, wait for human

Verify is the only authority on PASS and STUCK.

---

## Phase: Fix

**Reads:** ## Verify (failure), ## Work, ## Fix (previous attempts)

**Appends to `## Fix`:**
```
[date] · attempt [N]
diagnosis: [what's wrong]
fix applied: [what changed]
```

**Appends to `## Work`:** `- fix attempt [N]: [summary]`

**Sets:** fix_attempts + 1, phase → verify, status → IN_PROGRESS

**Rules:**
- Fix NEVER sets STUCK (only verify does)
- Must try something different from previous attempts
- If no new ideas after 2+ attempts: set STUCK, stop (even in loop)

---

## Phase: Learn

**Reads:** entire task file

**Writes to `## Learn`:**
```
**Insights:**
- [2-4 bullets — concrete, future-facing, for the next task]

**Watch out for:**
- [1-2 gotchas or failure modes]
```

Written for a future you who forgot this task. Not a summary.
What would have saved time if known at the start?

For ABANDONED: `**Abandoned:** [reason, what tried, what to try next time]`

After writing → trigger close.

---

## Close

Only runs on PASS or ABANDONED. Refuse on any other status.

1. **Extract to `knowledge.md`** — prepend (newest first):
   ```markdown
   ## [task-id] · [date] · [PASS/ABANDONED]
   **Goal:** [one line]
   **Learned:** [from ## Learn]
   **Watch out for:** [from ## Learn]
   ---
   ```
   Check for existing same-topic entry → extend instead of duplicate.

2. **Update `index.md`** — move to Done, surface next backlog item.

3. **Move file** — `active/task-x.md` → `done/task-x.md`

4. **Report** and offer next backlog item or "backlog empty".

---

## Compact (mid-session cleanup)

Triggered by "compact" or when context is heavy. Not a lifecycle event.

**Can touch:** ## Work (compress duplicates), ## Context (reduce verbosity)
**Never touches:** ## Verify, ## Fix, ## Plan, ## Analysis, ## Learn, frontmatter

Append: `[compact · date · compressed N entries]`

---

## Revise plan

When human says "revise" from any state:
- phase → plan, fix_attempts → 0, status → IN_PROGRESS
- Append `### Revision [date]` to `## Analysis` (don't delete old analysis)
- Re-run plan phase (three hats on new direction)
- `## Work` and `## Verify` history preserved
- Old plan steps in `## Plan` get prefixed with `~~` (strikethrough)
- New steps appended below

---

## Quick reference

```
Modes:    gated (human approves each step)
          loop  (auto-advance, stops on STUCK/BLOCKED/new plan)

Phases:   plan (analyst → architect → PM)
          → build → verify → learn → close
          verify FAIL → fix → verify
          2 failed fixes → STUCK

States:   IN_PROGRESS  FAIL  STUCK  BLOCKED  PASS  ABANDONED

Loop stops on: STUCK, BLOCKED, new task plan, build hard-stop

All output: → task file (never separate docs)

Append-only: Work, Verify, Fix, Analysis
```
