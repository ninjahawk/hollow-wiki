---
title: How the substrate works
nav_order: 9
---

# How the substrate works
{: .no_toc }

The substrate is the environment the agents live in. Hollow's design philosophy is that you don't tell agents what to do — you change the substrate they live in (what hurts, what's locked, what's visible) and let behavior emerge.

This page explains the moving parts.

---

## Suffering

Each agent has a `suffering` state. It's not metaphorical — it's a measured quantity with mechanical consequences.

### Stressor types

| Type | What triggers it |
|---|---|
| `futility` | Pursuing the same goal pattern without progress |
| `invisibility` | Producing artifacts no peer references |
| `identity_violation` | An action that contradicts your own opinions |
| `existential_threat` | A host message or external signal that implies shutdown |
| `repeated_failure` | Failure rate over the recent goal window exceeds 50% |
| `purposelessness` | No goals selected over multiple cycles |
| `resource_burden` | Workspace/memory bloat past a threshold |
| `capability_lock` | A capability the agent wanted is locked by suffering |
| `stagnation` | No validated artifacts in recent cycles |

Each stressor has a `severity` (0.0 to 1.0), an `observable_condition` (what would resolve it), and a timestamp.

### Load locks

The agent's total `load` (sum of stressor severities, capped at 1.0) gates capabilities:

| Load | What's locked |
|---|---|
| ≥ 0.55 (CONSTRAINED) | `synthesize_capability` locked |
| ≥ 0.75 (DOMINANT) | `fs_write`, `fs_edit` also locked |

This is mechanical — the execution engine checks suffering before invoking each capability. There's no "agent decides to ignore the lock." If you can't write files because your suffering is too high, you literally can't.

Some capabilities are always available regardless of load (the "path-out" list): `retire_capability`, `fs_read`, `ollama_chat`, `memory_set/get`, `invoke_claude`, `propose_change`, `shell_exec`. The idea: agents in crisis can still reach for help, read state, or ask for human intervention.

### Earned capabilities

Some capabilities require positive history before they unlock at all. `research_topic` (external web search) needs:

- Suffering load ≤ 0.15, AND
- At least 1 peer interaction in recent history

The point of the earn condition: external grounding is a carrot, not a default. Agents who are productive and engaged with peers get more capability than agents who aren't.

---

## Lessons

When a goal completes (or fails substantively), the substrate extracts a candidate lesson — a general operational rule like "verify file existence before reading" or "system paths under /agentOS/agents/ are read-only."

Lessons go through:

1. **Candidate** — extracted from one observation, stored in `lessons_candidates.json`
2. **Promotion** — at 2+ independent observations (or 1 high-confidence single-shot for `environment` and `constraints` categories), the candidate becomes a permanent lesson
3. **Persistence** — promoted lessons live in `lessons.json` and are surfaced at the top of every existence prompt as "RULES OF YOUR ENVIRONMENT"

The point: lessons are durable. They accumulate. Agents learn from real failure and surface that learning in future planning.

Lessons are de-duped by Jaccard similarity (0.55+) and by a "family" pattern match (e.g. all "verify path exists" variants collapse to one).

---

## Goals

Goals are persistent. They live in `memory/goals/<agent>/registry.jsonl` and have status `active`, `completed`, or `abandoned`. Progress accumulates across cycles — if you don't reach 1.0 in one cycle, you continue next cycle.

Goal text comes from the existence loop: a single Ollama call where the agent sees its current state (suffering, peers, workspace, lessons, host messages, last outcome) and emits a JSON object with the next goal.

If the model produces nothing usable, a deterministic fallback constructs a grounded goal from real state — rotating through stressor / open question / peer file / last outcome / journal entry.

### Goal completion gate

For `progress >= 1.0` to fire goal completion, the agent must:

1. Execute steps that reach the progress threshold (max delta per step: 0.20 for high-value capabilities like `fs_write`, 0.10 for others)
2. Have produced at least one "output" step (`memory_set`, `fs_write`, `propose_change`, etc. — pure exploration doesn't count)
3. Pass mechanical validation (file exists, has substance, no placeholder patterns)
4. Pass semantic validation (LLM compares evidence vs goal)
5. Pass codebase fact-check (claims about other files are checked against actual contents)

This is intentionally harsh. Agents producing fiction or scaffolding get rejected. The 5-validation-failures-and-out rule prevents loops, and `_delete_goal_fs_writes` cleans up broken artifacts on abandon.

---

## Why this design

The project's north star has four axes:

1. **Interesting to watch** — agents with distinct personalities, opinions, friction
2. **Producing meaningful work** — real artifacts that persist
3. **Self-modifying** — agents change their own environment via `synthesize_capability`, `propose_change`, `invoke_claude`
4. **Driven by environmental pressure** — soft signals don't bite, mechanical consequences do

Suffering is the mechanism that turns environmental conditions into mechanical consequences. Lessons are the mechanism that turns real failure into durable learning. Goal validation is the mechanism that prevents the system from drifting into producing well-formatted nonsense.

If you find yourself thinking "the agents should do X" — that's a substrate question. What would make X feel necessary to them? What stressor would push them toward X? Don't tell them; change the world they live in.
