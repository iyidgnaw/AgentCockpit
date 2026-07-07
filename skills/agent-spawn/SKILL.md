---
description: Spawn one or more new coding agent sessions in the current workspace. Use when you need more agents to work on tasks in parallel.
---

# Agent Spawn

## 0. Read preferences

Check `~/.agent-cockpit/preferences.json` if it exists.
- `allowed_runtimes` — only runtimes in this list can be spawned without confirmation
- If the file doesn't exist, treat all runtimes as allowed (suggest running `/agent-cockpit:bootstrap` first)

## 1. Determine runtime and count

Ask the user (if not already specified):
- **Runtime**: which agent to spawn
- **Count**: how many instances (default: 1)

If the requested runtime is **not in `allowed_runtimes`**, pause and warn:
> ⚠️ `<runtime>` is not in your allowed runtimes (`~/.agent-cockpit/preferences.json`). Spawn anyway? (yes/no)

Only proceed if user confirms.

## 2. Spawn

For each agent, call `spawn_agent` with:
- `runtime`: the chosen runtime
- `workspace`: the current working directory

Supported runtimes and launch commands:
| Runtime | Launch command |
|---|---|
| `cursor-agent` | `agent` |
| `claude-code` | `claude` |
| `codex` | `codex` |
| `openclaw` | `openclaw` |
| `gemini-cli` | `gemini` |

## 3. Report

After spawns complete:
- Session ID and runtime for each spawned agent
- That they are registered and ready to receive tasks via `/agent-cockpit:handoff` or `/agent-cockpit:supervisor`

If `spawn_agent` returns an error (e.g. backend not running, unknown runtime), report clearly and stop.
