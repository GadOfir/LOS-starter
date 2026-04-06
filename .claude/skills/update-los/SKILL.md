---
# LOS:managed — this file is replaced by /update-los
name: update-los
description: >
  Safely updates LOS from upstream (LOS-starter) while preserving user data.
  Triggers on "update", "update los", "check for updates", "upgrade", or /update-los.
metadata:
  phase: setup
  produces: Updated managed files + audit log entry
  depends-on: []
---

# Update LOS

Safely pulls updates from the upstream LOS-starter template into a user's
LOS instance. Preserves all user data (memory, tasks, logs, identity).

---

## Upstream source

- Repo: `GadOfir/LOS-starter`
- Branch: `master`
- Raw URL base: `https://raw.githubusercontent.com/GadOfir/LOS-starter/master/`

---

## What gets updated (managed files)

Any file containing `LOS:managed` in its first 5 lines is eligible for update.
This includes `CLAUDE.md`, `VERSION`, and any skill SKILL.md that has the marker.
User-created skills won't have this marker, so they're never touched.

---

## What NEVER gets touched

- `memory/` — all user data, identity, knowledge, projects
- `.tasks/` — task history, index, active work
- `logs/` — session logs
- User-created skills (any skill dir without `LOS:managed`)
- Any file not in the managed list above

---

## Bootstrap (getting update-los into an old LOS instance)

The update-los skill can't update itself into existence. For pre-v0.2.0 LOS
instances, the user must bootstrap by either:

1. **Copy the skill directory** from LOS-starter:
   ```
   cp -r <LOS-starter>/.claude/skills/update-los/ <LOS>/.claude/skills/update-los/
   ```
2. **Or tell Claude in the LOS repo:** "fetch the update-los skill from
   GadOfir/LOS-starter and install it" — Claude can fetch the raw file from GitHub.

After bootstrap, `/update-los` will detect the missing VERSION file and run
the first-time migration automatically.

---

## First-time migration (pre-v0.2.0 instances)

If the local repo has **no `VERSION` file**, this is a pre-v0.2.0 LOS instance
that needs a one-time migration before regular updates can work.

### Detection

Check these conditions:
- No `VERSION` file exists
- No `LOS:managed` markers in any files
- `## Identity` and/or `## State` sections exist in CLAUDE.md

If all true → run migration. If `VERSION` exists → skip to regular update flow.

### Migration steps

1. **Extract identity from CLAUDE.md** → create `memory/identity.md`:
   - Read `## Identity` section (Name, Role, Goal)
   - Read `## State` section (Projects, Active task, Last session, Last evolve)
   - Read `onboarded:` value
   - Write all to `memory/identity.md` using the template format:
     ```markdown
     # Identity

     - **Name:** {extracted}
     - **Role:** {extracted}
     - **Goal:** {extracted}

     `onboarded: {extracted}`

     # State

     - **Projects:** {extracted}
     - **Active task:** {extracted}
     - **Last session:** {extracted}
     - **Last evolve:** {extracted}
     ```

2. **Inventory custom skills** — scan `.claude/skills/` for any skill directory
   that does NOT exist in upstream (LOS-starter). These are user-created skills.
   List them and confirm they'll be preserved.

3. **Fetch and apply all managed files** from upstream — same as regular update
   Step 4, but for every managed file (since none exist locally yet).

4. **Create `VERSION` file** with upstream version.

5. **Audit log** — write migration entry:
   ```
   [update-los] First-time migration to v{version} — identity extracted to memory/identity.md, {N} managed files installed, {N} custom skills preserved
   ```

6. **Print summary:**
   ```
   ── Migration Complete ─────────────────────────
   ✓ Identity extracted to memory/identity.md
   ✓ {N} managed files installed (v{version})
   ✓ Custom skills preserved: {list or "none"}
   ──────────────────────────────────────────────
   ```

---

## Regular update flow

### Step 1: Check versions

1. Read local `VERSION` file
2. Fetch upstream `VERSION` from GitHub raw URL with cache-bust:
   ```
   curl -s "https://raw.githubusercontent.com/GadOfir/LOS-starter/master/VERSION?ts=$(date +%s)"
   ```
   Always append `?ts={unix_timestamp}` to force fresh fetch (bypass GitHub CDN cache).
3. Compare. If same → "Already up to date." → stop.
4. If upstream is newer → continue.

### Step 2: Fetch and diff

For each managed file:
1. Fetch the upstream version from GitHub
2. Diff against local version
3. Collect changes into a summary

### Step 3: Show preview

```
── LOS Update Available ───────────────────────
Local:    v{local}
Upstream: v{upstream}

Changed files:
  ~ CLAUDE.md (routing table updated)
  ~ .claude/skills/task-system/SKILL.md (new verify rules)
  + .claude/skills/new-skill/SKILL.md (new skill added)

Protected (untouched):
  ✓ memory/identity.md
  ✓ memory/ (all contents)
  ✓ .tasks/ (all contents)
  ✓ logs/ (all contents)

→ Apply update? (yes / no / show diffs)
───────────────────────────────────────────────
```

If user says "show diffs" → display each file diff inline.

### Step 4: Apply

1. For each changed managed file → replace local with upstream version
2. For new managed files → create them
3. Update local `VERSION` to match upstream
4. If upstream added new skill directories → create them

### Step 5: Audit

1. Log to `logs/{YYYY-MM-DD}.md`:
   ```
   [update-los] Updated from v{old} to v{new} — files changed: {list}
   ```
2. Print summary:
   ```
   ── Update Complete ────────────────────────────
   ✓ Updated v{old} → v{new}
   ✓ {N} files updated, {N} new files added
   ✓ User data untouched
   ──────────────────────────────────────────────
   ```

---

## Edge cases

- **User modified a managed file locally** — the update overwrites it. This is by
  design: managed files belong to the template. If users want custom behavior,
  they should create a new skill (not modify a managed one).
- **Upstream removes a managed file** — leave the local copy, flag it:
  `⚠ {file} no longer in upstream — consider removing`
- **Upstream adds a new managed file** — create it locally.
- **No network** — fail gracefully: "Can't reach upstream. Check connection."
- **Upstream CHANGELOG.md** — fetch and display relevant entries between
  local and upstream versions if available.

---

## Rollback

If something goes wrong:
- All managed files are in git. `git diff` shows exactly what changed.
- `git checkout -- {file}` restores any single file.
- The update itself should be a single commit for easy revert.
