# Second Brain Vault — Claude Code plugin

Claude Code plugin with reusable Obsidian vault skills for a PARA-style second brain.

**Version:** 0.1.0

**SYNC note:** Source of truth for skill text in a live vault is `claude-code-files/skills/`. This plugin folder is a snapshot — after editing vault skills, re-copy into `skills/` here.

## Skills (v1)

| Skill | Role |
|-------|------|
| `gate-orchestration` | Orchestrator + Judge multi-agent gates |
| `graphify-onboard` | Repo graph → vault project folder |
| `vault` | Session rituals, lessons, decisions, mirror sync |
| `opus-5-5-prompting` | Opus 5.5 prompt checklist |
| `diagrams-mermaid-draw-io` | Mermaid-first diagrams; draw.io optional |

No MCP servers, no `bin/`, no secrets.

## Prerequisites

- Claude Code CLI (or Claude web/phone for plugin upload)
- Obsidian vault mounted/synced on the machine (skills look for `$HOME/YourVault` / `$HOME/MyNotes` candidates)
- Optional deps some skills mention: `graphify` / `graphifyy`, Mermaid CLI (`mmdc`)

## Install — Claude Code (recommended)

### A. Try once (no install)

```bash
claude --plugin-dir /path/to/vault-template/claude-code-files/plugin/second-brain-vault
```

### B. User-scope install from local folder

In Claude Code: `/plugin` → Install from folder → point at this directory.
Reload plugins or start a new session after install.

### C. Symlink skills only (no plugin packaging)

```bash
PLUGIN=/path/to/second-brain-vault
mkdir -p ~/.claude/skills
for d in "$PLUGIN"/skills/*; do
  ln -sfn "$d" ~/.claude/skills/$(basename "$d")
done
```

## Install — Claude web / phone

1. Zip the plugin root (must include `.claude-plugin/plugin.json` and `skills/`).
2. In Claude: **Customize → Plugins** → upload / connect the plugin.
3. Enable **Second Brain Vault** for your account.

## Validate

```bash
cd /path/to/second-brain-vault
claude plugin validate .
```

## Not in this plugin

Personal decision inventories, account-tied NotebookLM workflows, course-specific tutors, and ops bots stay out of this public package.
