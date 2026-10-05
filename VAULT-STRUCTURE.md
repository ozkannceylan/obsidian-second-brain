# Vault structure (PARA)

Generic map for the template under `vault-template/`. Rename the vault root to whatever you use (`YourVault`, `MyNotes`, etc.).

## Top-level homes

| Folder | Job |
|--------|-----|
| `inbox/` | Capture only. Empty it regularly. |
| `journal/` | Day notes (`journal/YYYY/MonthName/YYYY-MM-DD.md`). Ops logs optional as `*-ops.md`. |
| `projects/active\|ideas\|on-hold\|completed/` | Work with an Index note per active project |
| `areas/` | Standing responsibilities (career, research, website, …) |
| `resources/` | Reference material, bookmarks, learning notes |
| `archive/` | Cold storage. Prefer archive over delete. |
| `assets/` | Images, attachments, media |
| `templates/` | Obsidian templates (daily note, project init, …) |
| `claude-code-files/` | Claude Code skills, example agent prompts (`agents/`), system docs, plugin snapshot |
| `rookie/` | Optional agent personality config — **do not modify** unless you own that harness |

Optional extras you may add locally (not required by this template): `wiki/` for a compiled knowledge graph, `Reviews/` for weekly syntheses, `Bases/` for Obsidian Bases schemas.

## Project lifecycle

```
ideas/ ──→ active/ ──→ completed/
              │
              └──→ on-hold/ ──→ active/ (resumed)
                       │
                       └──→ archive/ (abandoned)
```

Every **active** project folder typically contains:

- `Index.md` — summary, architecture, links
- `GRAPH_REPORT.md` — optional graphify structural analysis
- `wiki/` — optional community articles from graphify
- `lessons/`, `decisions/`, `gates/` — when using the vault / gate skills

## Capture → file loop

1. New stuff lands in `inbox/` (dated note + one-line why).
2. File it: idea → `projects/ideas/`; active work → `projects/active/<slug>/`; reference → `resources/`.
3. Durable knowledge (if you keep a wiki) compiles into typed pages with `[[wikilinks]]`.
4. Never dump durable notes at vault root.

## Hard rules (recommended)

1. **Never delete content files.** Move to `archive/` or prefix `_archived-`.
2. **Never write secrets to the vault.** No passwords, API keys, tokens, or recovery codes.
3. **One home per fact.** Prefer a single folder for each kind of note.
4. **Repos hold code; vault holds knowledge.** Build/test/lint stay in the repo’s `CLAUDE.md`; architecture and decisions live here.

## Security

- Secrets never in vault.
- Treat cloud sync as convenience, not an encrypted secret store.
