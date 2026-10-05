---
name: notebooklm
description: >-
  Create NotebookLM deliverables (video, audio, quiz, slides, mind map) from
  Obsidian vault content via notebooklm-py. Trigger on /notebooklm,
  "notebook-init", "generate-deliverable", or "sync-sources".
---

# /notebooklm — NotebookLM Vault Integration

Wraps the `notebooklm-py` CLI to automate deliverable creation (videos, podcasts, quizzes, slides, mind maps) from Obsidian vault content.

**Auth:** sign in with `notebooklm-py`'s own login flow on your machine. Never copy browser cookies or session files into the vault, a repo, or a prompt.

## Vault Path Detection

Discover `VAULT_ROOT` first (same order as `BASE_CLAUDE.md` / vault skill). Prefer **YourVault** (`$HOME/YourVault`), then legacy **MyNotes** candidates. All paths below are relative to `$VAULT_ROOT`.

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/notebooklm` | Show help |
| `/notebooklm notebook-init {project}` | Create notebook, upload all repo-mirror docs as sources |
| `/notebooklm generate-deliverable {project} {spec}` | Generate artifact from a DLVR spec file |
| `/notebooklm sync-sources {project}` | Add new repo-mirror files to existing notebook |

## Typical Workflow

```
1. Initialize notebook for your project (once)
   → /notebooklm notebook-init my-project
   (creates notebook, uploads the repo-mirror doc files, waits for indexing)

2. Define what you want to create
   → "define a deliverable — audio overview of module 3"
   (creates DLVR-001-module3-overview.md spec in deliverables/)

3. Generate it
   → /notebooklm generate-deliverable my-project deliverables/DLVR-001-module3-overview.md
   (runs generation, downloads artifact, updates spec status)

4. After adding new docs to the repo
   → /vault sync                                  # sync repo → vault mirror
   → /notebooklm sync-sources my-project          # add new files to notebook
```

## Deliverable Spec Format (DLVR)

Specs live in `$VAULT_ROOT/projects/active/{project}/deliverables/DLVR-{NNN}-{slug}.md`:

```yaml
---
id: DLVR-001
type: video | audio | slide-deck | quiz | mind-map | infographic
status: draft | prompt-ready | generated
notebook_id: null
date: YYYY-MM-DD
---
```

Body sections: Content Brief, Source Documents, Generation Config (Style, Instructions, Language), CLI Commands, Output.

## Supported Artifact Types

| Type | Output | Typical time |
|------|--------|-------------|
| `video` | .mp4 | 3-5 min |
| `audio` | .mp3 | 1-5 min |
| `slide-deck` | .pptx | 1-2 min |
| `quiz` | .json | 30-60 sec |
| `mind-map` | .json | 30-60 sec |
| `infographic` | .png | 1-2 min |

## Integration with /vault

- `/vault sync` updates the repo mirror → then `/notebooklm sync-sources` adds new files to the notebook
- `/vault end-session` captures deliverable status in `_index.md`
- Deliverable specs follow the DLVR format from `_meta/workflow-rules.md`

## Standalone Usage

Also works for one-off tasks without the vault (e.g., "create a podcast from these URLs"). See the `notebooklm-py` documentation for all artifact types and customization options.
