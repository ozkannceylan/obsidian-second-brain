---
name: graphify-onboard
description: This skill should be used when starting work on a new repository or project. It runs graphify to generate a structural knowledge graph, creates a project folder in the Obsidian vault, and populates it with the graph report, wiki articles, and an Index.md.
---

# Graphify Project Onboarding

Standard operating procedure for onboarding any repository into the Obsidian knowledge management system using graphify.

## When to Use

- Starting work on a new repository for the first time
- Re-onboarding a project after major structural changes
- When the user asks to "set up" or "onboard" a project

## Prerequisites

- `graphifyy` Python package installed (`pip install graphifyy`)
- `graphify claude install` run in the project root (adds CLAUDE.md section + PreToolUse hook)
- Obsidian vault accessible at the configured path

## Vault Path Detection

Discover `VAULT_ROOT` at session start (first match wins). Prefer **YourVault**, then legacy **MyNotes** candidates (same order as `BASE_CLAUDE.md`):

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

Store the path as `VAULT_ROOT`. Vault project folders live under `$VAULT_ROOT/projects/active/[project-name]/`.

## Onboarding Procedure

### Step 1: Install graphify for the project

Run in the project root:

```bash
graphify claude install
```

This adds a `## graphify` section to CLAUDE.md and registers a PreToolUse hook in `.claude/settings.json`.

### Step 2: Build the knowledge graph

On Windows, always set UTF-8 encoding to avoid charmap errors:

```bash
PYTHONUTF8=1 python -X utf8 -c "from graphify.watch import _rebuild_code; from pathlib import Path; _rebuild_code(Path('.'))"
```

On Linux:

```bash
python3 -c "from graphify.watch import _rebuild_code; from pathlib import Path; _rebuild_code(Path('.'))"
```

### Step 3: Generate wiki articles

```python
from graphify.wiki import to_wiki
from pathlib import Path
import json, networkx as nx

data = json.loads(Path('graphify-out/graph.json').read_text(encoding='utf-8'))
G = nx.node_link_graph(data, edges='links')

communities = {}
for node_id, node_data in G.nodes(data=True):
    comm = node_data.get('community', 0)
    communities.setdefault(comm, []).append(node_id)

community_labels = data.get('graph', {}).get('community_labels', None)
cohesion = data.get('graph', {}).get('cohesion', None)
god_nodes_data = data.get('graph', {}).get('god_nodes', None)

count = to_wiki(G, communities, Path('graphify-out/wiki'), community_labels, cohesion, god_nodes_data)
```

### Step 4: Create vault project folder

Create: `$VAULT_ROOT/projects/active/[project-name]/`

Use the repository directory name as `[project-name]`.

### Step 5: Copy outputs to vault

Copy these from `graphify-out/` to the vault project folder:

- `GRAPH_REPORT.md`
- `wiki/` directory (all community articles + index.md)

### Step 6: Create Index.md

Create `$VAULT_ROOT/projects/active/[project-name]/Index.md` with:

- YAML frontmatter: project name, status (in_progress), repo path, created date, tags
- Brief project description
- Architecture overview if applicable
- God Nodes section extracted from GRAPH_REPORT.md (top 8-10 most connected)
- Links to `[[GRAPH_REPORT]]` and `[[wiki/index|Wiki Index]]`
- Quick links: repo path, run commands, URLs

## Rebuilding

After major code changes, rebuild the graph:

```bash
PYTHONUTF8=1 python -X utf8 -c "from graphify.watch import _rebuild_code; from pathlib import Path; _rebuild_code(Path('.'))"
```

Then re-copy `GRAPH_REPORT.md` and regenerate wiki to update the vault.
