<!-- LOS:managed — this file is replaced by /update-los. User data lives in memory/identity.md -->
# Life Operating System

## Onboarding

> **If `memory/identity.md` has empty Identity fields** (no name/role/goal), run this
> onboarding flow instead of normal routing. Once complete, skip this section forever.

### Cold-start flow

Ask three questions, one at a time:

1. **What's your name?**
2. **What do you do?** (role — e.g., backend engineer, student, designer)
3. **What's the one thing you want LOS to help with first?** (e.g., "organize my side projects", "track learning goals")

Then:

1. Fill in Identity fields in `memory/identity.md`
2. Create the first project in `memory/projects/` using /build-project
3. Add that project to State in `memory/identity.md`
4. Write a `logs/{YYYY-MM-DD}.md` entry: `[onboarding] System initialized`
5. Set `onboarded: true` in `memory/identity.md`
6. Print: **"You're set up. Based on your goal, I'd suggest running `/task-system` to start your first task — or just tell me what you want to work on."**

> **Always read `memory/identity.md` at session start** for user identity and current state.

---

## Skills

| Skill | Triggers | Produces | Phase | Depends on |
|-------|----------|----------|-------|------------|
| task-system | "start session", "new task", "cold start", task states | Task files in `.tasks/` | daily | — |
| learn | "learn X", "research X", "study X" | Knowledge files in `memory/knowledge/` | daily | — |
| build-project | "new project X", "build X" | Project scaffold in `memory/projects/` | daily | — |
| evolve | "review", "status", "health check" | System health report + fixes | review | — |
| create-skill | "create skill", "new skill", "skill for X" | New skill in `.claude/skills/` | setup | — |
| update-los | "update", "upgrade", "check for updates" | Updated managed files + audit log | setup | — |

---

## Routing

Route user intent to the matching skill. If the user's words match a skill's
triggers (see table above), invoke that skill.

- "learn X" / "research X" → /learn
- "new project X" / "build X" → /build-project
- "review" / "status" → /evolve
- "start session" / "new task" → /task-system
- "create skill" / "new skill" / "skill for X" → /create-skill
- "update" / "upgrade" / "check for updates" → /update-los
- Unclear → ask what I'm trying to accomplish, then route

---

## Autonomy

**NEVER STOP:** Once the loop has begun (after initial plan approval), do NOT
pause to ask the human if you should continue. Do NOT ask "should I keep going?"
or "is this a good stopping point?". The human might be asleep, or away from the
computer and expects you to continue working indefinitely until manually stopped.
You are autonomous. If you run out of ideas, think harder — re-read files, try
combining previous near-misses, try more radical approaches. The loop runs until
the human interrupts you, period.

Only stop on:
- **STUCK** (2 failed fixes)
- **BLOCKED** (missing external dependency)
- **Task PASS** (done)

Mid-plan pauses to ask for approval are a bug, not a feature.

---

## Rules

1. Check memory/projects/ before creating anything new
2. Every project has: README.md (what+why), tasks.md, decisions.md
3. No secrets — 1Password references only
4. Log actions to `logs/{YYYY-MM-DD}.md` — format: `[{skill-name}] {action}`
5. Tasks use .tasks/ via /task-system
6. Knowledge goes to memory/knowledge/{topic}.md
7. Files over 200 lines → split or use supporting files

---

## Self-Healing (run during /evolve, not separately)

- .tasks/index.md out of sync → rebuild it
- Project missing README.md → flag it
- Logs gap > 3 days → note as "dark period"
- Knowledge files stale > 30 days → suggest refresh
- Orphan files in memory/ → flag for cleanup
- `memory/identity.md` State out of sync with filesystem → fix it
