# Obsidian Second Brain

A **public template** for an Obsidian vault used as a Claude Code–integrated second brain.

It ships a PARA-style folder layout, reusable Claude Code skills, a `BASE_CLAUDE.md` template for repos, and a small Claude plugin package. Copy the `vault-template/` tree into a new Obsidian vault and adapt paths to your machine.

## What this is

- Folder structure for capture → projects → areas → archive (PARA)
- Generic system docs (`SECOND_BRAIN.md`, `BASE_CLAUDE.md`)
- Claude Code skills: vault rituals, graphify onboarding, gate orchestration, Mermaid/draw.io diagrams, Opus prompting checklist
- A Claude plugin snapshot (`second-brain-vault`) with the same skills

## What this is not

- **Not** a personal vault dump. No journals, project notes, credentials, or private paths.
- **Not** a full Obsidian plugin for the desktop app — the included package is for **Claude Code** (`claude --plugin-dir …`).
- **Not** opinionated about which cloud sync you use (Obsidian Sync, Drive, etc.). Detection lists use generic `$HOME/YourVault` / `$HOME/MyNotes` candidates.

## Quick start

1. Clone this repo.
2. Copy `vault-template/` to where you keep Obsidian vaults, e.g. `$HOME/YourVault`.
3. Open that folder as a vault in Obsidian.
4. Point Claude Code at the vault: put a copy of `BASE_CLAUDE.md` content into each project’s `CLAUDE.md`, or symlink skills from `claude-code-files/skills/` into `~/.claude/skills/`.
5. Optional plugin try-once:

```bash
claude --plugin-dir /path/to/vault-template/claude-code-files/plugin/second-brain-vault
```

See [VAULT-STRUCTURE.md](VAULT-STRUCTURE.md) for the PARA map and hard rules.

## Included skills

| Skill | Role |
|-------|------|
| `vault` | Session start/end, lessons, decisions, repo→vault mirror sync |
| `graphify-onboard` | Build a code knowledge graph and create a vault project folder |
| `gate-orchestration` | Multi-agent Orchestrator + Judge gate protocol |
| `diagrams-mermaid-draw-io` | Mermaid-first diagrams; draw.io optional |
| `opus-5-5-prompting` | Prompting checklist for Claude Opus 5.5 |

## Intentionally excluded

Personal decision inventories, study tutors tied to private curricula, course-specific lesson packs, NotebookLM / Google-account workflows, and sync cutover notes. Those stay private.

## License

MIT — see [LICENSE](LICENSE).
