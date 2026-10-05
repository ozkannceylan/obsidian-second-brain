---
name: diagrams-mermaid-draw-io
description: >-
  Use this when creating or exporting flowcharts, architecture diagrams, or
  workflow visuals — choose Mermaid vs draw.io, export PNG/SVG, and fix edge
  routing.
---

# Diagrams: Mermaid + draw.io

## Default choice

- **Docs, CI, vault notes, quick auto-layout flows:** Mermaid (`.mmd` or fenced `mermaid` in markdown) — **prefer this first**.
- **Presentation / social / blog / human-facing PNG or SVG with hand-tuned layout:** draw.io **when installed** on the machine (optional).
- Prefer Mermaid source when the diagram must stay tiny and version-friendly; polish with draw.io when layout control matters (especially decision diamonds and feedback loops).

## Obsidian / living notes

Prefer **Mermaid inside the markdown note** (native or core Mermaid). It Syncs as text, edits with the note, and avoids binary churn.
Use **draw.io** in the vault only when you need a polished export (`.drawio` source + PNG/SVG attachment) for a presentation slide, blog cover, or hand-tuned layout. Do not replace every vault flowchart with PNG.

## Tooling (portable)

| Tool | Command / notes |
|------|-----------------|
| Mermaid CLI | `mmdc` if installed, else `npx --yes @mermaid-js/mermaid-cli` |
| draw.io | **Optional.** Use only if `drawio` / draw.io Desktop is on PATH. |
| MCP (optional) | `npx -y @drawio/mcp` when the user wants an interactive editor |

Do not assume machine-specific AppImage paths, `xvfb-run`, or hardcoded `~/.local/bin/drawio` locations.

## Steps — Mermaid (default)

1. Write `flowchart TD` (or LR) in a `.mmd` file or markdown fence.
2. Keep node labels short; color with `style` fills if useful.
3. Export when an image is requested:

```bash
mmdc -i diagram.mmd -o diagram.png -w 1200 -b white
# or:
npx --yes @mermaid-js/mermaid-cli -i diagram.mmd -o diagram.png -w 1200 -b white
```

(SVG: change output to `.svg`.)

4. For vault notes, paste the fence into the note; skip PNG unless the user asked for an image.

## Steps — draw.io (optional, when installed)

1. Author an uncompressed `.drawio` (mxGraph XML) **or** import Mermaid in the draw.io UI / CLI if available on that machine.
2. Hand-tune geometry for presentation quality.
3. **Feedback / loop edges:** enter the target from the correct side with horizontal approach.
   - Left-side entry pointing right: `entryX=0;entryY=0.5` and a waypoint at the target’s mid-Y **left of** the box (same Y as mid-height), not above the box. Avoid a final vertical segment along the left edge (arrow will look downward).
   - Example revise→vault: exit top of revise (`exitX=0.5;exitY=0`), one point at `(reviseCenterX, targetMidY)`, then into `entryX=0;entryY=0.5`.
4. Export PNG/SVG with the local draw.io CLI or Desktop Export menu (flags vary by install). Keep the `.drawio` as the editable source.
5. Show the PNG when the user asked for a visual.

## Do not

- Auto-publish diagrams to social/site without a fresh user approval that turn.
- Require machine-only AppImage / `xvfb-run` / hardcoded draw.io paths.
- Prefer draw.io PNG for every Obsidian note when a Mermaid fence would suffice.
