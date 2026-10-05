---
name: vault
description: >-
  Manage Obsidian vault project folders — session start/end rituals, repo-mirror
  sync, lessons, decisions, status, scan, and bootstrap. Trigger on /vault,
  "vault sync", "start-session", "end-session", "record lesson", or "record decision".
---

# /vault — Obsidian Vault Manager

A Claude Code skill that bridges your code repositories with the Obsidian vault for project tracking, decisions, lessons, and cross-project knowledge.

## Vault Path Detection

Discover `VAULT_ROOT` at session start (first match wins). Prefer a Sync/Drive mount named **YourVault**, then legacy **MyNotes** candidates (same order as `BASE_CLAUDE.md`):

```bash
for candidate in \
  "$HOME/YourVault" \
  "$HOME/MyNotes" \
  "$HOME/OneDrive/Documents/MyNotes" \
  "$HOME/Documents/MyNotes" \
  "G:/My Drive/MyNotes" \
  "$HOME/Google Drive/MyNotes" \
  "$HOME/google-drive/MyNotes" \
  "$HOME/My Drive/MyNotes" \
  "/mnt/google-drive/MyNotes"; do
  if [ -d "$candidate/projects" ] && [ -d "$candidate/claude-code-files" ]; then
    echo "VAULT_FOUND: $candidate"
    break
  fi
done
```

Store the path as `VAULT_ROOT`. All vault-relative paths below use `$VAULT_ROOT/...`.

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/vault` | Show help with all subcommands |
| `/vault sync` | Copy repo docs to vault mirror (one-way: repo → vault) |
| `/vault status` | Display the project's `_index.md` — current status at a glance |
| `/vault start-session` | Start ritual: load context, sync, scan patterns |
| `/vault end-session` | End ritual: log lessons, sync, update index |
| `/vault lesson <title>` | Record a lesson learned (bug fix, pattern, gotcha) |
| `/vault decision <title>` | Record a design decision with context and consequences |
| `/vault scan` | Find recurring patterns across all projects in the vault |
| `/vault bootstrap` | Initialize vault folder for a new repo |

## Typical Session Flow

```
1. Start coding session
   → /vault start-session
   (loads context, syncs docs, shows project status)

2. During work
   → /vault lesson "cache invalidation race on concurrent writes"
   → /vault decision "Choose B-tree over sorted list for index lookups"
   (log insights as they happen — never batch them)

3. End coding session
   → /vault end-session
   (writes pending lessons, syncs, updates index)
```

## How Mirror Sync Works

The sync copies documentation files from your repo into the vault's `repo-mirror/` folder so they're searchable in Obsidian and available on mobile via Sync.

**What gets synced:** README.md files, `docs/*.md`, `tasks/{ARCHITECTURE,LESSONS,PLAN,TODO}.md`, CLAUDE.md

**What doesn't sync:** Source code, tests, config files, media, build artifacts

**Direction:** Repo → vault only. Never writes back to the repo.

## Lessons & Decisions

**Lessons** capture bugs, patterns, and gotchas with Problem/Solution/Takeaway format. They use IDs like `PRJ-LES-001` (project prefix + sequential number).

**Decisions** follow a state machine: **Draft → Active → Superseded**. When a new decision replaces an old one, the old one gets marked as superseded with a link to its replacement.

### Automatic logging

Lessons and decisions are logged **automatically** during a session — no need to manually invoke `/vault lesson` or `/vault decision` each time:

- **Lessons**: When a bug is debugged/resolved or a non-obvious insight is discovered, Claude automatically writes the lesson file and briefly confirms: "Logged lesson: PRJ-LES-003 — cache invalidation race on concurrent writes"
- **Decisions**: When a design decision is confirmed in conversation ("yes let's do that", "go with X"), Claude automatically writes the decision file and confirms: "Recorded decision: PRJ-DEC-002 — Choose B-tree over sorted list"

You can still use `/vault lesson` or `/vault decision` manually to log something Claude didn't catch, or to edit the details before writing.

## File Locations

```
$VAULT_ROOT/                               ← Vault root (YourVault / MyNotes)
├── resources/
│   └── workflow/
│       ├── workflow-rules.md              ← Master workflow definition
│       ├── tech-patterns.md               ← Cross-project patterns
│       └── debug-playbook.md              ← Cross-project debug knowledge
├── claude-code-files/
│   └── skills/
│       └── vault/SKILL.md                 ← This file
└── projects/
    └── active/
        └── {project-name}/
            ├── _index.md                  ← Context entry point (read every session)
            ├── decisions/                 ← {PRJ}-DEC-NNN.md files
            ├── lessons/                   ← {PRJ}-LES-NNN.md files
            ├── drafts/                    ← WIP notes
            ├── architecture/              ← Design docs
            ├── deliverables/              ← Specs, outputs
            └── repo-mirror/               ← Synced repo docs
```

## Project Prefix Convention

The prefix is derived from the project name's initials:

| Project | Prefix |
|---------|--------|
| `my-cool-project` | `MCP` |
| `api-gateway` | `AGW` |
| `data-pipeline` | `DPL` |

Used in lesson and decision IDs: `MCP-LES-001`, `MCP-DEC-003`.
