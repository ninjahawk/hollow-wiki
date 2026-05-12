---
title: Updating
nav_order: 12
---

# Updating
{: .no_toc }

How to pull a new version, what survives an update, and what to do when behavior changes.

## The simple path

```bash
python3 hollow.py stop
git pull
docker compose pull   # if using GHCR-published images
python3 hollow.py
```

That's it for patch and minor version bumps (e.g. `5.7.x → 5.7.32`, `5.7 → 5.8`).

## What survives an update

- **Agent identity** (`memory/identity/<agent>/`), names, traits, opinions, narrative
- **Agent memory** (`memory/agents/`, semantic index), everything they've learned
- **Goal history** (`memory/goals/<agent>/registry.jsonl`)
- **Lessons** (`memory/identity/<agent>/lessons.json`)
- **Workspace files** (`workspace/<agent>/`)
- **Audit log** (`memory/audit.log`)
- **Config** (`config.json`)

## What gets reset

- **Container state**: anything in-memory dies on `docker compose down`. Agents re-cold-start their daemon loops.
- **Active goals in flight**: if you stop mid-cycle, that cycle's work is rolled back via txn rollback. Agents pick up from the last persisted state on restart.

## When to nuke

A "nuke" wipes agent state and starts fresh. Use it when:

1. **Baseline is contaminated.** An early bad goal cascade poisoned the agent's worldview and they keep returning to the same loop. Symptoms: same goal text appearing in 10+ cycles, suffering stuck at high load, no new artifacts.
2. **Major version bump.** Releases that change identity structure, lesson schemas, or capability registration will mention "memory reset required" in the release notes.
3. **You want to see the cold-start emergence.** First-cycle agents pick names, set initial opinions, draft early goals. Watching this from scratch is worth doing once.

How to nuke:

- **Via panel**: click the **Nuke** button. Confirms before acting.
- **Via shell** , 

  ```bash
  python3 hollow.py stop
  rm -rf memory/audit.log memory/goals/* memory/identity/* memory/agents/* memory/semantic-index.json
  python3 hollow.py
  ```

Workspace files and `config.json` survive a nuke. If you want a TRULY clean slate, also delete `workspace/<agent>/*`.

## Reading release notes

Each release on [GitHub releases](https://github.com/ninjahawk/hollow-agentOS/releases) has notes. Read them before updating, especially for minor bumps (5.7 → 5.8). The pattern:

- **Patch** (`5.7.31 → 5.7.32`): bug fixes, internal refactors. Safe to pull without thinking.
- **Minor** (`5.7 → 5.8`): new features, changed defaults. May require config tweak. Notes will say.
- **Major** (`5.x → 6.x`): substrate changes. Memory reset likely required.

## What if behavior changes after an update?

First check the release notes. Most changes are explicitly called out.

Common change-after-update patterns:

- **"My agents complete more goals now"**: that's intentional, v5.7.32 fixed the completion math; before that, `progress >= 1.0` was mathematically unreachable in many configurations.
- **"My agents pick different fallback goals"**: also intentional, v5.7.32 fixed the deterministic fallback that produced the same text every cycle for the same stressor.
- **"Validation now rejects things it used to accept"**: placeholder gate widened in v5.7.32. Files with `[From LLM Output]`, `[Hypothesis]`, etc, patterns are caught now.
- **"Cycles take longer"**: probably the cycle worker timeout bumped from 600s → 1500s to accommodate qwen3.6's real cycle lengths. Each cycle has more headroom, completes more often.

## Downgrading

```bash
git checkout v5.7.31   # or whatever tag
docker compose down
docker compose up -d --build
```

Memory and identity from a higher version may not be readable by a lower version. Check the release notes for forward/backward compat, most patch releases are bidirectional, minor and major are not.

## Reporting regressions

If an update made things worse, [report it](https://github.com/ninjahawk/hollow-agentOS/issues/new) with:

- Old version, new version
- What changed (specific behavior)
- Logs from before AND after (`docker logs hollow-api`)
- Whether you nuked or carried memory forward
