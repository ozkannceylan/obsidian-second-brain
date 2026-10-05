---
name: gate-orchestration
description: Use when a project is run as a multi-agent build - Claude acts as Orchestrator and Judge, decomposes work into gates, dispatches parallel subagents, verifies their reports against a fixed rubric, and records a gate summary in the Obsidian vault. Trigger on "gate", "orchestrate", "delegate to agents", "multi-agent", or when a project's ORCHESTRATION.md declares this protocol.
---

# Gate Orchestration — Orchestrator + Judge Protocol

Deterministic multi-agent execution. The orchestrator decides and verifies; subagents
produce. Every unit of progress is a **gate** with falsifiable exit criteria and a written
summary in the vault.

## Hard Rules

1. **The orchestrator never writes feature code.** It writes contracts, verdicts, and vault
   summaries only. Exception: config/scaffold files under 20 lines.
2. **No gate closes without evidence.** Command output, test results, file diffs. "Should
   work" is a FAIL.
3. **The judge re-verifies independently.** Never accept a subagent report at face value.
   Confirm deliverable files exist and re-run at least one evidence command.
4. **Tasks inside one gate must be independent.** No shared files, no sequential deps.
   Anything sequential becomes the next gate.
5. **Max 2 rework rounds per task.** Then stop and escalate to the user with the blocker.
6. **Max 5 agents per gate.** More than that stops being verifiable.

---

## Agent Roster (fixed)

| Agent | `subagent_type` | Writes? | Job |
|-------|-----------------|---------|-----|
| **Scout** | `Explore` | no | Search, map, locate. Returns findings, not opinions. |
| **Architect** | `Plan` | no | Interface + data-flow design. Returns a spec, no code. |
| **Builder** | `general-purpose` | yes | Implements exactly one module against a spec. |
| **Verifier** | `general-purpose` | tests only | Adversarially tries to break a Builder's output. |
| **Scribe** | `general-purpose` | docs only | Turns accepted work into vault/repo documentation. |

Builder and Verifier for the same module never run in the same gate — verification of an
unfinished thing is theatre.

---

## Gate Lifecycle (7 steps, in order)

### 1. DEFINE

Write `$VAULT_ROOT/projects/active/[project]/gates/G{n}-{slug}.md` with `status: open` before
dispatching anything. Must contain: objective, **falsifiable exit criteria**, agent task
list, required evidence.

### 2. DECOMPOSE

Split into independent tasks. For each, name the single file/module it owns. If two tasks
touch the same file, merge them into one task or split across two gates.

### 3. DISPATCH

Launch **all** agents for the gate in a **single message with multiple Agent tool calls** so
they run simultaneously. Each agent receives the Task Contract below verbatim.

### 4. COLLECT

Wait for all reports. A missing report is a FAIL, not a reason to guess.

### 5. JUDGE

Score every report against the Rubric. Apply the deterministic verdict rule.

### 6. RECONCILE

Integrate PASS work. Re-dispatch FIX/FAIL tasks with the specific defect named. Resolve
cross-agent conflicts yourself — do not ask agents to negotiate.

### 7. RECORD

Write the gate summary (template below), flip `status: passed`, update the project
`Index.md` gate ledger and the repo's `tasks/TODO.md`. Only then open the next gate.

---

## Task Contract (paste into every agent prompt)

```
TASK ID:      G{n}-T{k}
OBJECTIVE:    <one sentence, one outcome>
INPUTS:       <exact file paths / prior gate artifacts to read first>
OWNS:         <the only files you may create or modify>
DELIVERABLE:  <exact output paths>
DONE WHEN:    <falsifiable condition, e.g. "pytest tests/test_x.py passes, 0 failures">
FORBIDDEN:    Touching files outside OWNS. Installing packages without saying so.
              Claiming success without command output. Inventing APIs, paths, or fields
              you have not read.

REPORT BACK EXACTLY THIS:
STATUS:       DONE | PARTIAL | BLOCKED
DELIVERABLES: <paths you actually wrote>
EVIDENCE:     <commands you ran + their real output, verbatim>
DECISIONS:    <choices you made and why>
RISKS:        <what may be wrong with this>
UNVERIFIED:   <claims you did NOT prove — be honest, empty is suspicious>
```

---

## Judge Rubric (5 checks)

| # | Check | Fails when |
|---|-------|-----------|
| 1 | **Scope** | Files outside `OWNS` were modified |
| 2 | **Evidence** | A success claim has no command output behind it |
| 3 | **Correctness** | `DONE WHEN` is not actually met |
| 4 | **Consistency** | Conflicts with another agent's output or a prior `DEC` |
| 5 | **Honesty** | Fabricated path/API, or `UNVERIFIED` implausibly empty |

**Verdict rule (deterministic):**

- 5/5 pass → **PASS** — integrate.
- Fails 3 or 4 only → **FIX** — re-dispatch with the defect named.
- Fails 1, 2, or 5 → **FAIL** — discard the output, re-dispatch from scratch.

Judge independently: `ls` the deliverables, re-run one evidence command, `git diff --stat`
to confirm scope.

---

## Gate Summary Template

Short by design — one screen. This is the presentation ledger.

```markdown
---
title: G{n} — {Gate Name}
project: {project}
gate: G{n}
date: YYYY-MM-DD
status: passed | failed | open
tags: [gate, {project}]
---

# G{n} — {Gate Name}

**Objective:** one sentence.
**Verdict:** PASS (N/N tasks) | FAILED — reason

## What was built
- bullet per deliverable, with the file path

## Evidence
| Check | Command | Result |
|-------|---------|--------|
| ... | `...` | ... |

## Decisions
- [[{PRJ}-DEC-NNN]] — one line

## Risks / carried forward
- one line each

## Next gate
G{n+1} — {name}: one sentence.
```

---

## Failure Handling

| Situation | Action |
|-----------|--------|
| Agent returns BLOCKED | Orchestrator resolves the blocker, then re-dispatches |
| Two agents conflict | Orchestrator decides, records a `DEC`, re-dispatches the loser |
| Same task fails twice | Stop. Escalate to the user with the exact blocker |
| Evidence cannot be reproduced | Treat as FAIL regardless of what the report said |
| Scope creep detected | Discard, re-dispatch with tightened `OWNS` |

---

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

Gate files live at `$VAULT_ROOT/projects/active/[project]/gates/`. In templates below, `[VAULT]` means `$VAULT_ROOT`.

## Session Start Protocol

1. Discover `VAULT_ROOT` (detection list above).
2. Read `$VAULT_ROOT/projects/active/[project]/Index.md` → current gate.
3. Read the newest `$VAULT_ROOT/projects/active/[project]/gates/G*.md` → what passed, what carried forward.
4. Read `$VAULT_ROOT/projects/active/[project]/lessons/` → known traps.
5. Resume at the open gate. Never skip ahead.
