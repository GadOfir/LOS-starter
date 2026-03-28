---
name: build-project
description: >
  Creates a new project in memory/projects/ with standard structure.
  Triggers on "new project", "build X", "start project", or /build-project.
argument-hint: "[project name]"
---

# Build Project

1. Check `memory/projects/` — already exists?
2. Yes → show it, ask if update
3. No → create `memory/projects/{name}/`:
   - `README.md`: what, why, constraints, status
   - `tasks.md`: what | why | priority 1-3 | status
   - `decisions.md`: empty with header template
4. For code projects → init `.tasks/` using task-system skill
5. Cross-reference `memory/knowledge/` for related topics
6. Log: `[build-project] Created: {name}`
