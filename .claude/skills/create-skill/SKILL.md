---
# LOS:managed — this file is replaced by /update-los
name: create-skill
description: >
  Creates new Claude Code skills for LOS following Skills 2.0 conventions.
  Triggers on "create skill", "new skill", "add skill", "make a skill",
  "skill for X", "automate X as a skill", or /create-skill.
argument-hint: "[description of what the skill should do]"
metadata:
  phase: setup
  produces: New skill directory in .claude/skills/
  depends-on: []
---

# Create Skill

## Step 1: Gather intent

Ask (skip any the user already answered):

```
What should this skill do? (one sentence)
```

Then determine:

- **Name**: kebab-case, action-oriented (e.g., `deploy-checker`, `pr-reviewer`)
- **Triggers**: what phrases should activate it
- **Scope**: does it need `paths` to limit where it loads?

## Step 2: Design

Before writing, plan:

1. **Reads** — which files/directories/APIs?
2. **Writes** — where does output go?
3. **Outputs** — what does the user see?
4. **Guard rails** — what should it NOT do?

One skill = one job. If it does two things, make two skills.

## Step 3: Create `.claude/skills/{name}/SKILL.md`

Use this structure:

```yaml
---
name: {name}
description: >
  {Third-person description. What it does + when to trigger.}
argument-hint: "{hint}" # optional, shown in autocomplete
# Optional fields — include only when needed:
# context: fork          # run in isolated subagent
# agent: Explore         # subagent type
# allowed-tools: Read, Grep, Bash(npm *)  # restrict tools
# paths: "src/**"        # only load when editing matching files
# disable-model-invocation: true  # user-only, no auto-trigger
---

# {Name}

{Direct, imperative instructions. Numbered steps.
Keep under 100 lines — use supporting files for detail.}
```

### Rules for the skill content:

- **Third-person descriptions** — injected into system prompt as-is
- **Direct style** — no filler, numbered steps
- **Progressive disclosure** — keep SKILL.md lean, put reference material in sibling files
- **Log step required** — every skill logs: `[{skill-name}] {action}`
- **No command wrapper needed** — the skill name IS the slash command

## Step 4: Wire routing (if needed)

Add a line to `CLAUDE.md` Routing section only if the skill has natural-language triggers that aren't obvious from the description:

```
- "{trigger phrase}" → /name
```

## Step 5: Log and report

Log: `[create-skill] Created: {name} — {description}`

```
── Skill created ──────────────────────────────
Name: {name}
Path: .claude/skills/{name}/SKILL.md
Trigger: "{example phrase}" or /{name}
───────────────────────────────────────────────
```

## Rules

- Never overwrite an existing skill without asking
- If an existing skill already does this → offer to extend it instead
- Don't create skills for one-off tasks
- Don't create mega-skills — split if > 2 responsibilities
- Don't create `.claude/commands/` wrappers — skills ARE commands in 2.0
