---
description: Show the current status of all coding agents running in terminal sessions. Use when you need to see what agents are available, what they're working on, and which are idle, busy, or blocked.
---

# Agent Inspect

1. Call `discover_agents` with `workspace` set to the current working directory. This probes idle sessions in the same workspace and updates the registry.
2. Call `list_agents` to get the full picture from the registry.

Format the result as a status table:

| Agent ID | Runtime | Status | Repo | Branch | Task Hint |
|---|---|---|---|---|---|
| {agent_id} | {runtime} | {availability} | {repo or —} | {branch or —} | {task_hint or —} |

After the table, summarize in plain language:
- Who is **available** (ready for a new task)
- Who is **busy** (currently working — do not interrupt)
- Who is **blocked** (waiting for approval or in error state — may need attention)

If no agents are found, say "No agent sessions detected. Make sure other agents are running in iTerm2 panes."
