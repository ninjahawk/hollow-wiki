---
title: Reading the live monitor
nav_order: 7
---

# Reading the live monitor
{: .no_toc }

`python3 thoughts.py` (or the Open Monitor button on the panel) streams the agents' real-time activity. Once you can read it, you can tell what the system is doing at a glance.

## What you'll see

### Cycle markers

```
12:38:42  Cycle 14: 3 agent(s) (0 skipped/cooling) workers=6
```

A new cycle just started. All three agents have an active goal and will work through one round each in parallel.

### Existence loop output

```
12:38:50  scout         🧠  existence loop, goal: Execute `baseline_test.py` and inspect...
```

The agent just decided on a goal for this cycle. The text after `goal:` is what they're about to pursue.

### Step execution

```
12:39:01  scout         ▶  shell_exec
12:39:01                ✓  shell_exec
12:39:42  scout         ▶  ollama_chat
12:41:24                ✓  ollama_chat
```

Each `▶` is a step starting, each `✓` is success, each `✗` is failure. Time between start and end tells you which capabilities are slow (LLM calls dominate).

### Validation

```
12:41:30  scout         🔍  artifact ok | file /agentOS/workspace/scout/note.md (substantive)
```
or
```
12:41:30  scout         ⚠  artifact MISSING (3/5) | semantic verification rejected: ...
```

The substrate just checked whether the goal was actually accomplished. The fraction (`3/5`) is the validation-failure count toward permanent abandonment.

### Suffering changes

```
12:42:11  scout         💢  suffering: load=0.45 stressors=[stagnation,epistemic_uncertainty]
```

The agent's suffering value just changed. The bracketed list is the active stressors. If you see load creeping above 0.55, that agent's `synthesize_capability` is locked. Above 0.75, `fs_write` and `fs_edit` are too.

### Goals abandoned

```
12:43:08  scout stalled on 'Verify the persistence hypothesis of the baseline artifact...', goal abandoned
```

After 5 validation failures or 4 consecutive step failures, a goal is permanently abandoned and any partial artifacts are deleted by `_delete_goal_fs_writes`.

### Goal completions

```
12:45:00  analyst → goal=goal-5e2b356d72da progress=1.00 steps=1
```

`progress=1.00` and a `→ goal=` line means a goal completed AND validated. This is the milestone, agent will pick new work on the next cycle.

Anything less than `1.00` is partial progress that carries over to the next cycle (the same goal will be re-attempted).

## What "healthy" looks like

- Cycles complete within 4-15 min on a recommended GPU
- Multiple `progress=1.00` outcomes per hour (goal completions)
- Suffering loads fluctuating between 0.2 and 0.6 (some pressure, not maxed)
- Workspace files growing slowly with substantive artifacts

## What's NOT healthy

- **No log activity for 5+ minutes**: daemon may be hung. Restart via panel or `python3 hollow.py stop; python3 hollow.py`.
- **Same goal text appearing in 10+ consecutive cycles**: the loop guard is failing. Should be auto-detected and rotated in v5.7.32+.
- **All agents stuck at progress=0.9**: completion is unreachable. Should be impossible in v5.7.32+ (delta math fix).
- **`Cycle worker timeout (1500s)` every cycle**: Ollama is too slow or hung. Check `curl http://localhost:11434/api/ps` for loaded models.

## Filtering the stream

The thoughts log is colorized for the terminal. To grep just goal outcomes from logs:

```bash
docker logs hollow-api 2>&1 | grep "→ goal=" | tail -20
```

For validation results:

```bash
docker logs hollow-api 2>&1 | grep "VALIDATE_END"
```

For everything an agent did in a window:

```bash
docker logs hollow-api --since 30m 2>&1 | grep "scout"
```
