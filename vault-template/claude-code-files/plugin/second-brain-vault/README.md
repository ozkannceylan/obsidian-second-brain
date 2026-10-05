# Second Brain Vault — Claude Code plugin

Claude Code plugin with reusable Obsidian vault skills for a PARA-style second brain.

**Version:** 0.2.0

**SYNC note:** Source of truth for skill text in a live vault is `claude-code-files/skills/`. This plugin folder is a snapshot — after editing vault skills, re-copy into `skills/` here.

## Skills

| Skill | Role |
|-------|------|
| `vault` | Session rituals, lessons, decisions, mirror sync |
| `graphify-onboard` | Repo graph → vault project folder |
| `gate-orchestration` | Orchestrator + Judge multi-agent gates |
| `diagrams-mermaid-draw-io` | Mermaid-first diagrams; draw.io optional |
| `opus-5-5-prompting` | Opus 5.5 prompt checklist |
| `adhd-study-tutor` | One-command-per-message study tutor with quiz gates |
| `robotics-lessons` | Retrieve verified robotics/controls/PLC lessons before guessing |
| `notebooklm` | NotebookLM deliverables (video, audio, quiz, slides) from vault docs |

No MCP servers, no `bin/`, no secrets.

## Prerequisites

- Claude Code CLI (or Claude web/phone for plugin upload)
- Obsidian vault mounted/synced on the machine (skills look for `$HOME/YourVault` / `$HOME/MyNotes` candidates)
- Optional deps some skills mention: `graphify` / `graphifyy`, Mermaid CLI (`mmdc`), `notebooklm-py` (signed in with its own login flow)

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

Personal decision inventories, private course material, sync cutover notes, and ops/deployment skills (chat-bot bridges, server access, publishing and upload automations, cookie-based CLI wrappers) stay out of this public package.

Project agent prompts (for example the AMR roster in `../../agents/amr/`) are not part of the plugin; copy them into a repo's `.claude/agents/`.
