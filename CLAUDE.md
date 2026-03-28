# Life Operating System

## Routing
- "learn X" / "research X" → /learn
- "new project X" / "build X" → /build-project
- "review" / "status" → /evolve
- "start session" / "new task" → /task-system
- "create skill" / "new skill" / "skill for X" → /create-skill
- Unclear → ask what I'm trying to accomplish, then route

## Rules
1. Check memory/projects/ before creating anything new
2. Every project has: README.md (what+why), tasks.md, decisions.md
3. No secrets — 1Password references only
4. Log actions to `logs/{YYYY-MM-DD}.md` — format: `[{skill-name}] {action}`
5. Tasks use .tasks/ via /task-system
6. Knowledge goes to memory/knowledge/{topic}.md
7. Files over 200 lines → split or use supporting files

## Self-Healing (run during /evolve, not separately)
- .tasks/index.md out of sync → rebuild it
- Project missing README.md → flag it
- Logs gap > 3 days → note as "dark period"
- Knowledge files stale > 30 days → suggest refresh
- Orphan files in memory/ → flag for cleanup
