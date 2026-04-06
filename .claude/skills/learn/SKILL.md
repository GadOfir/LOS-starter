---
# LOS:managed — this file is replaced by /update-los
name: learn
description: >
  Captures and structures knowledge into memory/knowledge/.
  Handles two modes: topic learning and source ingestion.
  Triggers on "learn", "study", "research", "deep dive", "understand",
  "explain X", "ingest", "read this", "process this", or when /learn is invoked.
argument-hint: "[topic or URL/file path]"
metadata:
  phase: daily
  produces: Knowledge files in memory/knowledge/
  depends-on: []
---

# Learn

Two modes — detect automatically from input:

- **Topic mode:** user says "learn about X" or "explain X" → structured knowledge page
- **Source mode:** user provides a URL, file path, or pastes content → ingest and integrate

---

## Mode 1: Topic

1. Check `memory/knowledge/{topic}.md` — if exists, extend it
2. If new, create with this format:

```markdown
## {Topic}
Last updated: {YYYY-MM-DD}

### TL;DR
2-3 sentences.

### Core Concepts
3-7 items max. Not a textbook.

### Connects To
Links to other knowledge files, projects, areas.

### Gotchas
What trips people up.

### Resources
Links with WHY each is useful.
```

3. **Run cross-update** (see below)
4. Cross-reference with projects if relevant
5. Log: `[learn] {topic}`
6. If topic is deep enough, suggest creating a project for it

---

## Mode 2: Source Ingestion

For when the user provides a URL, file, or pasted text to process.

### Step 1: Read the source

- **URL** → fetch and read (use WebFetch)
- **File path** → read from disk
- **Pasted text** → use directly

### Step 2: Discuss

Before filing anything, share 3-5 key takeaways with the user.
Ask: "Anything you want me to emphasize or skip?" — then proceed.
If the user said "just ingest it" or similar, skip discussion and file directly.

### Step 3: Write source summary

Create `memory/knowledge/sources/{source-name}.md`:

```markdown
## {Source Title}
Source: {URL or file path or "pasted text"}
Ingested: {YYYY-MM-DD}

### Summary
3-5 sentences capturing the core argument/content.

### Key Points
- Bulleted list of the most important ideas, facts, or claims.

### Connects To
Links to other knowledge files this source is relevant to.

### Quotes / Data
Notable quotes, statistics, or data points worth preserving verbatim.
```

### Step 4: Run cross-update (see below)

### Step 5: Log

`[learn] ingested: {source name}`

---

## Cross-update (runs in both modes)

This is what makes knowledge compound. After writing/updating the primary file:

1. **Scan `memory/knowledge/`** — read existing files (skip the file you just wrote)
2. **For each existing file**, check if the new information is relevant:
   - Does it add to, clarify, or contradict something in that file?
   - Does it introduce a new connection worth noting?
3. **If yes** — update that file:
   - Add new info under the appropriate section
   - Add a link in `### Connects To` pointing to the new/updated file
   - If new info **contradicts** an existing claim, flag it:
     `> ⚠ Contradicted by [{source}]({path}) ({date}) — {brief explanation}`
   - Update `Last updated:` date
4. **If no relevant files exist** — that's fine, just the primary file is enough

### Cross-update rules

- Don't force connections — only update files where the relevance is clear
- Keep updates surgical — add a bullet or a link, don't rewrite sections
- Preserve existing content — append, don't replace
- When in doubt about a contradiction, flag it rather than silently overwriting

---

## Knowledge index

After any write to `memory/knowledge/`, update `memory/knowledge/index.md`:

```markdown
# Knowledge Index

| File | Summary | Updated |
|------|---------|---------|
| [topic.md](topic.md) | One-line description | YYYY-MM-DD |
| [sources/name.md](sources/name.md) | One-line description | YYYY-MM-DD |
```

- One row per file, sorted by last updated (newest first)
- Create `index.md` if it doesn't exist
- This helps cross-update: read index first to find relevant files, then drill in
