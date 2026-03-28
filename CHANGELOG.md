# Changelog

All notable changes to LOS-starter are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/).

---

## [0.3.0] - 2026-03-29

### Changed
- CLAUDE.md Skills table replaced with scan directive — skills auto-discovered from `.claude/skills/*/SKILL.md`
- CLAUDE.md Routing replaced with scan directive — skills declare their own triggers
- update-los no longer warns about custom CLAUDE.md entries (no longer needed)
- update-los managed files list is now dynamic (any file with `LOS:managed` marker)

---

## [0.2.0] - 2026-03-29

### Added
- `/update-los` skill — safely pull upstream updates while preserving user data
- `VERSION` file for tracking LOS-starter version
- `memory/identity.md` — user identity and state now live in memory (protected from updates)
- `CHANGELOG.md` (this file)
- `LOS:managed` markers on all updatable files
- Autonomy reminder in task-system loop mode section
- `target_repo` pre-check in task-system build phase
- Cross-repo task support in task-system

### Changed
- `CLAUDE.md` is now fully updatable — identity/state moved to `memory/identity.md`
- Onboarding flow writes to `memory/identity.md` instead of CLAUDE.md
- All skills that read/write State now point to `memory/identity.md`
- Self-healing checks reference `memory/identity.md`

### Breaking
- `## Identity` and `## State` sections removed from CLAUDE.md
- Existing LOS instances must migrate identity data to `memory/identity.md`

---

## [0.1.0] - 2026-03-28

### Added
- Initial LOS-starter template
- Core skills: task-system, learn, build-project, evolve, create-skill
- BMAD-inspired plan phase (analyst → architect → PM)
- Loop mode with autonomous execution
- Landing page (index.html)
