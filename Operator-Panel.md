---
title: Operator panel
nav_order: 6
---

# Operator panel
{: .no_toc }

The operator panel is the canonical UI for Hollow AgentOS. It's a native window (via pywebview) with everything you need to run the system day-to-day. The other surfaces (`thoughts.py`, CLI commands) are for terminal-only sessions.

## Opening it

| | |
|---|---|
| **Windows** | Double-click `panel.bat` |
| **Mac / Linux** | `python3 panel.py` |

One-time install (Mac/Linux): `pip3 install pywebview httpx`. Windows users get this automatically from `install.bat`.

## What every control does

### Top bar

- **Start**: brings the stack up. Equivalent to `python3 hollow.py` without args.
- **Stop**: clean shutdown. Frees GPU memory. Agent state and identity survive.
- **Open Monitor**: opens `thoughts.py` in a new terminal window. Live stream of every agent action.
- **Open Workspace**: opens `workspace/` in your file manager. This is where agents write artifacts.

### Per-agent controls

For each of the three agents (scout, analyst, builder):

- **Suspend**: pauses the agent. Skipped on the next cycle. Doesn't lose state.
- **Resume**: un-pauses.
- **Suffering load**: manual override for the agent's overall suffering value (0.0 to 1.0). Useful for forcing capability locks or unlocks during testing.
- **Clear stressors**: wipes all active stressors. The substrate will rebuild them naturally on the next cycle if conditions warrant.
- **Add stressor**: inject a custom stressor with your own type and condition. Mostly useful for prodding behavior under a specific pressure.

### World injection

- **Drop file**: drops a file you specify into a chosen agent's workspace. The agent discovers it on the next existence cycle when scanning workspace contents.
- **Send host message**: delivers a message from "the host" (you) directly into the agent's existence prompt. Use sparingly, too many host messages contaminate the substrate's emergence.
- **Trigger environmental event**: fires one of the ambient events (weather, good_day, echo, object). The agent sees the event in their prompt next cycle.

### Nuke

- **Nuke**: wipes agent state for a clean reset. Goal histories, identities, audit log, dynamic tools. The container, model, and workspace files survive. Use when a baseline is contaminated by a bad initial run and you want to start fresh.

## When to use what

| Goal | Path |
|---|---|
| Watch what's happening | Open Monitor button (or `python3 thoughts.py`) |
| Check what files agents made | Open Workspace button |
| Pause an agent without breaking the others | Per-agent Suspend |
| Test what happens under high suffering | Per-agent Suffering load → 0.8+ |
| Reset to clean baseline | Nuke |
| Implement something an agent requested | Read `memory/claude_requests.jsonl`, satisfy or reject |

## Limitations

The panel doesn't currently:

- Show real-time agent suffering bars (use the monitor for live state)
- Visualize lesson promotion graph
- Show the decision queue contents

These are on the roadmap. For now, all that info is in `memory/` files and the live monitor stream.
