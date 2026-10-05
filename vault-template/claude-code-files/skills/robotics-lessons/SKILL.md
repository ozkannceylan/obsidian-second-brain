---
name: robotics-lessons
description: Search the verified engineering lessons corpus when stuck on a robotics, controls, PLC, or simulation problem: unexpected runtime behavior, cryptic errors, wrong physics, failing training or deployment (MuJoCo, ROS 2, Pinocchio, ACT/VLA, Isaac Lab, Siemens PLC/TIA, CAN, Gazebo). Use before web search or guessing when debugging domain-specific issues. Do not use for general knowledge questions.
---

# Robotics Lessons Retrieval

Corpus: verified, provenance-anchored lessons from past robotics
projects, one note per lesson. Location: `$VAULT_ROOT/wiki/lessons/` (read-only).

## Vault Path Detection

Discover `VAULT_ROOT` first (same order as `BASE_CLAUDE.md`). Prefer **YourVault** (`$HOME/YourVault`), then legacy **MyNotes** candidates. Lessons live at `$VAULT_ROOT/wiki/lessons/`.

Legacy fallbacks if the preferred mount is missing:
- `$HOME/OneDrive/Documents/MyNotes/wiki/lessons/`
- `$HOME/Documents/MyNotes/wiki/lessons/`
- `G:/My Drive/MyNotes/wiki/lessons/`

## Suggested lesson format

Each lesson note: id (`LL-...`), Symptom (verbatim error strings), Root cause,
Rule, Scope and limits, Provenance (repo + commit). `INDEX.md` holds one line
per lesson: id + rule.

## Protocol

1. Grep first: `rg -i "<distinctive error token or API name>"` inside
   `$VAULT_ROOT/wiki/lessons/`. Symptom sections contain verbatim error strings, so
   paste the exact error text when you have one.
2. No hit: read `$VAULT_ROOT/wiki/lessons/INDEX.md` (one line per lesson) and match
   the observed symptom against the rule lines semantically.
3. Open the matching note. Apply the Rule. Check Scope and limits
   before applying; a rule outside its scope is a new bug.
4. Cite the lesson id (LL-...) in your output whenever you use one.
5. Nothing matches: say so plainly. Never force-fit a lesson.

## Closing the loop

If you solve a novel, non-obvious domain problem that cost real
debugging time and no lesson covered it, suggest to the user recording
it in the repo's LESSONS.md. That file feeds the next corpus
ingestion.
