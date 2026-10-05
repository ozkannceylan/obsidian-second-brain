# Templates

Short generic Obsidian templates. Copy into your vault’s Templates folder and bind them in Obsidian settings.

## Daily note

```markdown
---
title: "{{date:YYYY-MM-DD}}"
date: {{date:YYYY-MM-DD}}
tags: [journal, daily]
---

# {{date:YYYY-MM-DD}}

## Intent
-

## Notes
-

## Wins
-

## Carry forward
-
```

## Project init (`projects/active/<slug>/Index.md`)

```markdown
---
title: "<Project name>"
status: in_progress
created: YYYY-MM-DD
tags: [project, active]
---

# <Project name>

**Repo:** `/path/to/repo`
**Status:** in progress

## Summary
One paragraph.

## Architecture
-

## Quick links
- [[GRAPH_REPORT]] (optional)
- [[wiki/index|Wiki Index]] (optional)

## Gate ledger (optional)
| Gate | Status |
|------|--------|
| G1 — … | open |
```

## Lesson stub

```markdown
---
title: "<PREFIX>-LES-NNN — short title"
date: YYYY-MM-DD
tags: [lesson]
---

# <PREFIX>-LES-NNN — short title

**Problem:** …
**Solution:** …
**Takeaway:** …
```

## Decision stub

```markdown
---
title: "<PREFIX>-DEC-NNN — short title"
status: active
date: YYYY-MM-DD
tags: [decision]
---

# <PREFIX>-DEC-NNN — short title

**Context:** …
**Decision:** …
**Consequences:** …
```
