---
title: What this is
nav_order: 2
---

# What this is
{: .no_toc }

Hollow AgentOS is a runtime for three local LLM agents that share a workspace, pick their own goals, and develop over time. They run on `qwen3.6:35b-a3b` (or a smaller fallback) on your hardware. No cloud calls. You set it up, leave it running, and observe.

The closest existing categories are "agent framework" and "autonomous-agent demo," but neither fits well. Frameworks are toolkits you build with; Hollow is a running system you live alongside. Demos are short-lived; Hollow is designed for sustained operation over weeks. Closer in spirit to a habitat than either.

---

## What's in the box

Three agents (scout, analyst, builder, named by themselves on first boot), an API on `:7777`, a tool store, and an operator panel. About 30 base capabilities; agents can also synthesize new ones at runtime and hot-load them without restarts.

The state that matters lives in `memory/`:
- `memory/identity/<agent>/` for names, opinions, narrative, lessons
- `memory/goals/<agent>/registry.jsonl` for goal history
- `memory/claude_requests.jsonl` for implementation requests the agents file for you
- `memory/audit.log` for every capability call

The workspace lives in `workspace/<agent>/`. Artifacts that pass validation stay there across cycles.

---

## How the substrate works

Each agent has a `suffering` value composed of stressors (futility, repeated_failure, stagnation, capability_lock, etc.). It's a quantity, not a metaphor: load >= 0.55 locks `synthesize_capability`, load >= 0.75 also locks `fs_write` and `fs_edit`. The execution engine checks suffering before invoking each capability. There is no "agent decides to ignore the lock" path.

Goals come from an existence prompt that describes state (suffering, peers, workspace, lessons, last outcome) and asks for the next goal. The prompt does not tell the agent what to value or what good output looks like. Behavior comes from the substrate, not from prescription.

Goal completion goes through a five-layer gate: file substance (AST checks, placeholder patterns), modify-intent (did the goal say it would touch a specific path and did it), semantic check (LLM compares evidence vs goal text), codebase fact-check (claims about other files are checked against actual contents), and peer feedback. Failures up to five times before permanent abandon. Broken artifacts get deleted on abandon; substantive ones stay.

Lessons get extracted from validation outcomes and promoted at >=2 observations (or one high-confidence single-shot for `environment` and `constraints`). Promoted lessons surface at the top of every existence prompt as durable rules. Identity, goal history, and lessons all persist across restarts.

---

## Design axes

These are the constraints every change is checked against:

1. **Interesting to watch.** Three agents with developing personalities, voice, friction, drift over time.
2. **Work that persists.** Artifacts survive cycles. Workspace accumulates rather than empties.
3. **Self-modifying.** `synthesize_capability` adds new tools at runtime. `propose_change` submits system-code edits for peer review. `invoke_claude` files implementation requests for the operator.
4. **Environmental pressure, not instruction.** Mechanical consequences. Soft signals get ignored. Lessons accumulate from real failure.

---

## Where this is

Core functionality works. Agents pick goals, complete some, abandon most, generate real artifacts, accumulate lessons, occasionally file `invoke_claude` requests.

Still to build:

- Sustained unattended operation over weeks (currently measured in hours and days)
- Reliable use of `synthesize_capability` and `propose_change` (the capability exists; agents use it rarely)
- Personality depth that holds up over hundreds of hours
- Cooperative/adversarial peer dynamics beyond message-passing

The design axes are descriptions and aspirations at once. The interesting parts of building this are the parts not yet working.

---

## Where to go from here

- [Setup]({{ '/Setup.html' | relative_url }}): install it, watch it run
- [Reading the live monitor]({{ '/Live-Monitor.html' | relative_url }}): how to read the activity stream
- [How the substrate works]({{ '/Substrate.html' | relative_url }}): suffering, stressors, locks, lessons, full detail
- [The operator panel]({{ '/Operator-Panel.html' | relative_url }}): every control reference

[Get started]({{ '/Setup.html' | relative_url }}){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[Report a bug](https://github.com/ninjahawk/hollow-agentOS/issues/new){: .btn .fs-5 .mb-4 .mb-md-0 }
