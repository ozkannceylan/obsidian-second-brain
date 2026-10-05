---
title: Second Brain System — Claude Code Integration
description: How an Obsidian vault works as a centralized knowledge system and how Claude Code interacts with it across projects and devices.
---

# Second Brain System

This Obsidian vault is the single source of truth for knowledge, projects, decisions, and documentation. Claude Code operates as an extension of this system — reading from it for context, writing to it for persistence, and keeping it current as projects evolve.

## Vault Structure (PARA Method)

```
YourVault/   # or MyNotes — whatever you name the vault root
├── inbox/              Unsorted captures, quick notes, clippings
├── journal/            Monthly journals and daily logs
├── projects/           All project work, organized by lifecycle
│   ├── active/         Currently in progress — has deliverables and deadlines
│   ├── ideas/          Proposals, explorations, not yet committed
│   ├── on-hold/        Paused — waiting on something external
│   └── completed/      Done — moved here when shipped
├── areas/              Ongoing responsibilities with no end date
│   ├── career/         Career development, skills
│   ├── research/       Research topics and papers
│   └── website/        Personal or product site notes
├── resources/          Reference material, learning notes, bookmarks
├── archive/            Cold storage — anything no longer active
├── assets/             Images, attachments, media files
├── templates/          Obsidian templates (daily notes, project init, etc.)
├── claude-code-files/  Claude Code skills, configs, and this file
│   └── skills/         Reusable skills for Claude Code
└── rookie/             Agent personality config (do NOT modify)
```

## Project Lifecycle

```
ideas/ ──→ active/ ──→ completed/
              │
              └──→ on-hold/ ──→ active/ (resumed)
                       │
                       └──→ archive/ (abandoned)
```

Every **active** project folder typically contains:

- `Index.md` — Project summary, architecture, god nodes, quick links
- `GRAPH_REPORT.md` — Graphify structural analysis (optional)
- `wiki/` — Auto-generated community articles from graphify (optional)

## Project Onboarding (standard for all repos)

When starting work on any new repository:

1. **Graphify** — Build knowledge graph (`graphify claude install` + rebuild)
2. **Wiki** — Generate wiki articles from the graph
3. **Vault folder** — Create `projects/active/[project-name]/`
4. **Copy** — `GRAPH_REPORT.md` + `wiki/` into the vault folder
5. **Index.md** — Project summary with god nodes, architecture, and links

See `claude-code-files/skills/graphify-onboard/SKILL.md` for the full procedure.

## Documentation Principles

- **Vault is truth** — Architectural decisions, project summaries, and structural knowledge live here, not scattered in repos
- **Graphify is structure** — Code understanding comes from the knowledge graph; don’t re-derive what graphify already mapped
- **Index.md is the entry point** — Every project folder has one; it links to everything else
- **Wiki for depth** — Community articles explain subsystems; god nodes show core abstractions
- **Repos hold code, vault holds knowledge** — The repo’s `CLAUDE.md` has build/test/lint commands and code patterns; the vault has the why, the architecture, and the cross-project context

## Cross-Project Awareness

The vault gives Claude Code something no single repo can: context across all projects.

- `projects/active/` shows what’s being worked on simultaneously
- `areas/` shows ongoing responsibilities that may influence priorities
- `projects/ideas/` shows what’s coming next
- `resources/` has reference material that may apply to any project

## Claude Code Files

`claude-code-files/` is Claude Code’s own workspace in the vault:

- **skills/** — Reusable skills that work across all projects (source of truth)
- **plugin/second-brain-vault/** — Claude plugin snapshot; try with `claude --plugin-dir …/second-brain-vault`
- This file (`SECOND_BRAIN.md`) — System documentation
- `BASE_CLAUDE.md` — Template prepended to every project’s `CLAUDE.md`
