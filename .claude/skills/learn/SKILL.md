---
name: learn
description: >
  Captures and structures knowledge into memory/knowledge/.
  Triggers on "learn", "study", "research", "deep dive", "understand",
  "explain X", or when /learn is invoked.
argument-hint: "[topic]"
---

# Learn

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

3. Cross-reference with projects if relevant
4. Log: `[learn] {topic}`
5. If topic is deep enough, suggest creating a project for it
