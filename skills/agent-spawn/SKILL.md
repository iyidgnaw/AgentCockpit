---
description: Spawn one or more new coding agent sessions in the current workspace. Use when you need more agents to work on tasks in parallel.
---

# Agent Spawn

Ask the user (if not already specified):
- **Runtime**: which agent to spawn — `cursor-agent`, `claude-code`, `codex`, or `gemini-cli`
- **Count**: how many instances (default: 1)

Then for each agent to spawn, call `spawn_agent` with:
- `runtime`: the chosen runtime
- `workspace`: the current working directory

Supported runtimes and their launch commands:
| Runtime | Launch command |
|---|---|
| `cursor-agent` | `agent` |
| `claude-code` | `claude` |
| `codex` | `codex` |
| `gemini-cli` | `gemini` |

After all spawns complete, report:
- Session ID and runtime for each spawned agent
- That they are now registered in the workspace registry and ready to receive tasks via `/agent-cockpit:handoff` or `/agent-cockpit:supervisor`

If `spawn_agent` returns an error (e.g. iTerm2 not running, unknown runtime), report it clearly and stop.
