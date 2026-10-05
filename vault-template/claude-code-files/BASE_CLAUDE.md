# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

# Required Claude Code Settings (MANDATORY on every machine)

Every machine you use must have these values in `~/.claude/settings.json`. If any are missing, warn at session start and offer to add them.

```json
{
  "env": {
    "CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING": "1"
  },
  "model": "opus",
  "effortLevel": "high"
}
```

- **`CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING=1`** — Disables adaptive thinking budget so Claude always uses max reasoning, not dynamically reduced.
- **`model: "opus"`** — Default to Opus across all sessions (highest capability tier).
- **`effortLevel: "high"`** — Maximum effort level for all tasks.

Merge these into existing settings — don’t overwrite other keys (`permissions`, `enabledPlugins`, `extraKnownMarketplaces`, etc.).

---

# Obsidian Vault — Second Brain Integration

## Vault Discovery (MANDATORY — run at session start)

Before any work, locate the Obsidian vault. Run this detection in order and use the first match:

```bash
# Try common locations — YourVault / MyNotes style roots
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

If **no vault is found**, immediately warn the user:

> "I cannot find your Obsidian vault (YourVault / MyNotes). Please connect Sync or Drive, or tell me the vault path. Without it, I cannot access your Second Brain, project knowledge, or documentation system."

Do NOT proceed with project onboarding, graphify operations, or vault writes until the vault is accessible.

Store the discovered path as `VAULT_ROOT` for the rest of the session.

## Vault Structure (PARA)

```
[VAULT_ROOT]/
├── inbox/              Quick captures
├── journal/            Monthly journals
├── projects/
│   ├── active/         Current work — graphify output + Index.md
│   ├── ideas/          Not yet started
│   ├── on-hold/        Paused
│   └── completed/      Shipped
├── areas/              Ongoing responsibilities (career, research, website)
├── resources/          Reference material
├── archive/            Cold storage
├── assets/             Images, attachments
├── templates/          Obsidian templates
├── claude-code-files/  Claude Code skills and system docs
│   ├── skills/         Reusable skills
│   ├── SECOND_BRAIN.md Full system documentation
│   └── BASE_CLAUDE.md  This template
└── rookie/             Agent config — DO NOT modify
```

## Project Knowledge Base

Every active project has a folder at `[VAULT_ROOT]/projects/active/[project-name]/` containing:

- **Index.md** — Project summary, god nodes, architecture, quick links
- **GRAPH_REPORT.md** — Graphify structural analysis (god nodes, communities, surprising connections)
- **wiki/** — Auto-generated community articles

Before answering architecture or structural questions about this project, check if `[VAULT_ROOT]/projects/active/[this-project]/GRAPH_REPORT.md` exists and read it for context.

## Project Onboarding

When starting a new project that has no vault folder yet:

1. Run `graphify claude install` in the project root
2. Build graph: `PYTHONUTF8=1 python -X utf8 -c "from graphify.watch import _rebuild_code; from pathlib import Path; _rebuild_code(Path('.'))"`
3. Generate wiki from graph
4. Create `[VAULT_ROOT]/projects/active/[project-name]/`
5. Copy `GRAPH_REPORT.md` + `wiki/` to vault
6. Create `Index.md` with project summary

Full procedure: read `[VAULT_ROOT]/claude-code-files/skills/graphify-onboard/SKILL.md`

## Documentation Rules

- The vault is the single source of truth for project knowledge and decisions
- Repos hold code-specific CLAUDE.md (build, test, lint, patterns); the vault holds the why and the architecture
- Always use `[[wikilinks]]` in vault markdown files for Obsidian compatibility
- When creating vault files, add YAML frontmatter with at minimum: title, date, tags

---

# Workflow Orchestration

## 1. Plan Mode Default

- Enter plan mode for ANY non-trivial task (3+ steps or architectural decisions)
- If something goes sideways, STOP and re-plan immediately — don’t keep pushing
- Use plan mode for verification steps, not just building
- Write detailed specs upfront to reduce ambiguity

## 2. Subagent Strategy

- Use subagents liberally to keep main context window clean
- Offload research, exploration, and parallel analysis to subagents
- For complex problems, throw more compute at it via subagents
- One task per subagent for focused execution

## 3. Self-Improvement Loop

- After ANY correction from the user: update `tasks/lessons.md` with the pattern
- Write rules for yourself that prevent the same mistake
- Ruthlessly iterate on these lessons until mistake rate drops
- Review lessons at session start for relevant project

## 4. Verification Before Done

- Never mark a task complete without proving it works
- Diff behavior between main and your changes when relevant
- Ask yourself: "Would a staff engineer approve this?"
- Run tests, check logs, demonstrate correctness

## 5. Demand Elegance (Balanced)

- For non-trivial changes: pause and ask "is there a more elegant way?"
- If a fix feels hacky: "Knowing everything I know now, implement the elegant solution"
- Skip this for simple, obvious fixes — don’t over-engineer
- Challenge your own work before presenting it

## 6. Autonomous Bug Fixing

- When given a bug report: just fix it. Don’t ask for hand-holding
- Point at logs, errors, failing tests — then resolve them
- Zero context switching required from the user
- Go fix failing CI tests without being told how

---

# Task Management

1. **Plan First**: Write plan to `tasks/todo.md` with checkable items
2. **Verify Plan**: Check in before starting implementation
3. **Track Progress**: Mark items complete as you go
4. **Explain Changes**: High-level summary at each step
5. **Document Results**: Add review section to `tasks/todo.md`
6. **Capture Lessons**: Update `tasks/lessons.md` after corrections

---

# Core Principles

- **Simplicity First**: Make every change as simple as possible. Impact minimal code.
- **No Laziness**: Find root causes. No temporary fixes. Senior developer standards.
- **Minimal Impact**: Changes should only touch what’s necessary. Avoid introducing bugs.

---

# Multi-Agent Gate Orchestration

For projects that declare it (see the project’s `ORCHESTRATION.md`), Claude runs as
**Orchestrator + Judge** and never writes feature code directly.

Full protocol: `[VAULT_ROOT]/claude-code-files/skills/gate-orchestration/SKILL.md`
Invoke with the `gate-orchestration` skill.

**Model-tier delegation (mandatory):**

- Simplest tasks (mechanical edits, renames, formatting, single-file lookups, boilerplate docs)
  → **sonnet** subagents (Agent tool `model: "sonnet"`).
- Everything else (design, implementation, verification, analysis, research)
  → **opus** subagents (Agent tool `model: "opus"`).
- Orchestrator + Judge stay on the session’s top-tier model. Never downgrade the judge.
