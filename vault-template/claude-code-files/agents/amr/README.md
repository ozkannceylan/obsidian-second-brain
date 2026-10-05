# AMR agent roster (example)

Claude Code subagent prompts from an autonomous mobile robot (AMR / forklift) project: a ROS 2 vehicle stack, a Siemens PLC with a safety program, an OPC UA signal bridge, a VDA 5050 fleet manager, a commissioning HMI and a Gazebo simulation.

They show one way to split a multi-layer robotics repo across single-responsibility agents that work under an Orchestrator + Judge model (see the `gate-orchestration` skill).

## Use

Copy the files into your repository's `.claude/agents/` folder (example repo name: `your-org/amr-agent`) and adapt directory names, ADR numbers and invariant numbers to your own `CLAUDE.md`.

Every agent assumes the repository provides:

- `CLAUDE.md` — the contract: §2 invariants, §5 agent roster, §6 gate criteria, §8 ADR format, §9 domain conventions
- `docs/LESSONS.md` — project lessons, read at startup
- `docs/briefs/` — one brief per task, with a `forbidden` list
- `docs/reports/` — one report per brief (brief, status, files_changed, invariants_touched, open_questions, next_suggested)

## Roster

| Agent | Writes only | Responsibility |
|-------|-------------|----------------|
| `agv-ros2` | `agv/` | VDA 5050 client node and Nav2 bridge (ROS 2) |
| `arch-docs` | `docs/adr/`, `docs/roadmap.md`, `docs/PLAN.md` | ADRs, roadmap and plan upkeep |
| `bridge` | `bridge/` | Gazebo ↔ PLC signal bridge (ROS 2 → OPC UA), no logic |
| `fleet` | `fleet/` | Fleet manager, MQTT and OPC UA clients |
| `hmi` | `hmi/` | Commissioning HMI, OPC UA client of the PLC |
| `infra` | paths named in the brief | Cross-cutting plumbing, toolchain, environment |
| `interface` | `docs/interfaces/` | VDA 5050 subset, OPC UA node model, handshakes |
| `plc` | `plc/` | Standard and safety program specs, TIA Portal exports |
| `safety-spec` | `docs/safety/` | Safety requirements spec and validation (ISO 13849) |
| `sim` | `sim/` | Gazebo worlds, launch files, test scenarios |
| `verifier` | report only | Read-only gate verifier: invariants, gates, boundaries |

Shared rules across the roster: safety never traverses the network, agents never commit (the orchestrator commits by pathspec), and invariant changes go through an ADR proposal instead of being implemented.
