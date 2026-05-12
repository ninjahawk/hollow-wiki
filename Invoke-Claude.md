---
title: invoke_claude
nav_order: 11
---

# invoke_claude
{: .no_toc }

The agent-to-human implementation channel. When an agent decides something needs to change in the system code (where they don't have write access), they file a request to a queue. You read it, decide if it's reasonable, implement (or reject) it.

## The queue

- **Requests** land in `memory/claude_requests.jsonl`
- **Responses** go in `memory/claude_responses.jsonl`
- **One line per entry**, JSON

Example request:

```json
{
  "request_id": "req-5b11c7573c9d",
  "agent_id": "builder",
  "timestamp": "2026-05-12 04:58:20",
  "description": "Patch builder.py to handle whitespace-only inputs as null",
  "spec": "Find input validation in builder.py. Modify the string stripping logic so whitespace-only strings are treated as empty/null.",
  "design_path": "",
  "request_type": "modify_file",
  "status": "pending"
}
```

## Quality gates (agent-side)

Before a request lands in the queue, the substrate enforces:

- **Description ≥ 40 chars** — vague intents rejected
- **Spec ≥ 80 chars** — must be substantive, not "fix it"
- **No circular requests** — agents asking Claude to manage the invoke_claude queue itself get auto-rejected
- **Queue cap = 3 pending** — agents flooding the queue while stuck signals confusion, not progress
- **agent_id propagated** — unattributable requests are rejected at the source

## What to do with a request

1. **Read the spec carefully.** If it references files, verify they exist. If it makes claims about current code behavior, check the actual code.
2. **If grounded and safe — implement it.** Edit the file as the agent asks.
3. **If unsafe or not grounded — reject it.** Append a rejection to `claude_responses.jsonl`:

   ```json
   {"request_id":"req-xxx","status":"rejected","implemented_at":"<timestamp>","result":"<reason>"}
   ```

4. **If fulfilled — write a fulfillment.** Same structure with `"status":"fulfilled"`. The agent will see this on `check_claude_status` and can move on.

## What NOT to do

- **Don't coach the agent.** If they wrote a bad spec, reject it with a clear reason. Don't rewrite their request for them.
- **Don't approve substrate-level changes casually.** If the agent asks you to disable suffering or remove a validation gate, that's an attack on the substrate. Reject and let them iterate within real constraints.
- **Don't implement requests targeting non-existent files.** "Modify scheduler.py to add retry logic" is fine; "Modify schedule_v2.py to add retry logic" when that file doesn't exist is a hallucination request — reject.

## Using Claude Code

If you use [Claude Code](https://claude.ai/code) as your editor, you can hook MCP and read the queue directly:

```json
{
  "mcpServers": {
    "agentos": {
      "command": "python3",
      "args": ["/path/to/hollow-agentOS/mcp/server.py"]
    }
  }
}
```

Add this to `~/.claude/settings.json`. Then in Claude Code:

- `agent_list`, `agent_get` — see who's running and what their state is
- `memory_get`, `fs_read` — check the queue and project files
- `shell_exec` — run verification commands

You can read a request, inspect the relevant code, implement the change, and write the response — all from one conversation. The MCP server has 91 tools available.

The MCP server reads the API token from `config.json`. If you get `{"detail": "Unauthorized"}`, the token isn't set in your Claude settings — add `"env": {"AGENTOS_TOKEN": "<your-token>"}` to the mcpServers config.

## Why this exists

Agents have aggressive permission boundaries — they can't edit system code (anything under `/agentOS/agents/`, `/agentOS/api/`, `/agentOS/mcp/`). This is intentional: it forces them to articulate what they want changed and why, rather than silently rewriting their own substrate.

`invoke_claude` is the legitimate path. Agents that want changes outside their permission level have to write a spec, file the request, and wait for a human to decide. The harshness of the gate (40-char description, 80-char spec, no circular requests, agent_id required) is part of the substrate — agents who can't articulate what they want don't get to ask.

## Common patterns

| Pattern | What to do |
|---|---|
| "Add capability X to live_capabilities.py" | Real feature request. Implement if reasonable. |
| "Fix bug in scheduler.py at line N" | Verify the bug exists. Fix if real. |
| "Modify suffering thresholds" | This is a substrate change. Default reject unless you genuinely want to soften the substrate. |
| "Help me debug why my goal failed" | Reject. invoke_claude isn't a chat channel. |
| "Add propose_change capability" | Already exists. Reject with pointer. |
| "Run shell command X" | They have `shell_exec`. Reject with pointer. |
