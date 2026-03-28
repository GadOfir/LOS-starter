# Life Operating System (LOS)

A markdown-native life management system powered by [Claude Code](https://claude.ai/code).

Track projects, capture knowledge, manage tasks, and evolve your system — all through natural conversation with Claude.

## Quick Start

```bash
# 1. Clone this repo
git clone https://github.com/YOUR_USERNAME/LOS.git
cd LOS

# 2. Open Claude Code
claude

# 3. Say one of these:
#    "start session"          → set up your first task
#    "new project my-app"     → register a project
#    "learn kubernetes"       → research and capture knowledge
#    "review"                 → system health check
#    "create skill for X"    → add a new skill
```

That's it. Claude reads `CLAUDE.md` for routing and uses the skills automatically.

## What You Get

### 5 Skills (Skills 2.0 format)

| Skill | Trigger | What it does |
|-------|---------|--------------|
| `/learn` | "learn X", "research X" | Captures structured knowledge to `memory/knowledge/` |
| `/build-project` | "new project X", "build X" | Creates project with README, tasks, decisions |
| `/task-system` | "start session", "new task" | Single-task-at-a-time workflow (plan → build → verify → learn) |
| `/evolve` | "review", "status" | System health check + improvement suggestions |
| `/create-skill` | "create skill", "new skill" | Creates new skills following Skills 2.0 conventions |

### Structure

```
CLAUDE.md               → Routing rules + system rules
.claude/skills/         → All 5 skills (Skills 2.0 format)
memory/
  projects/             → One folder per project (README, tasks, decisions)
  knowledge/            → Structured knowledge files
  areas/                → Life areas (career, health, etc.)
  decisions/            → Cross-cutting decisions
  entities/             → Accounts, people, systems
logs/                   → Daily action logs
.tasks/                 → Active task (created on first use)
.claude.los             → Cross-project pointer (copy to other repos)
```

## Cross-Project Integration

Copy `.claude.los` into any other project's root. Claude will:
- Log actions back to your central LOS
- Pull context from your knowledge and project files
- Never write to LOS memory from other projects (only append to logs)

## Customization

### Add your identity
Edit `~/.claude/CLAUDE.md` (your global Claude config) with:
- Your name, role, expertise
- Preferences for how Claude should work with you
- Links to your accounts (no secrets — use 1Password references)

### Add life areas
Create files in `memory/areas/` for things you manage:
```markdown
# Career
## Current
Your role @ Company
## Goals
What you're working toward
## Open Questions
Things to figure out
```

### Create new skills
Say "create skill for deploying my app" and the skill-creator will:
1. Ask what it should do
2. Design it with proper Skills 2.0 frontmatter
3. Wire it into CLAUDE.md routing
4. Give you a trigger phrase to test

## Design Principles

1. **One file = one truth** — Tasks, knowledge, and projects live in single files, not scattered docs
2. **Skills 2.0** — All skills use Claude Code's latest format (frontmatter, argument-hints, discovery)
3. **No commands directory** — Skills ARE commands. `/learn` invokes the `learn` skill directly
4. **Self-healing** — `/evolve` detects and fixes structural issues automatically
5. **Cross-project** — `.claude.los` connects any repo to your central LOS
6. **No secrets** — Use 1Password (or your vault) references only

## License

MIT
