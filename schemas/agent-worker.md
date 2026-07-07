# Agent Worker

An Agent Worker represents a coding agent running in a terminal session, as returned by `list_agents`.

## Fields

| Field | Type | Values | Description |
|---|---|---|---|
| `agent_id` | string | e.g. `w1:t2:p0` | iTerm2 session identifier — use this as `session_id` in MCP calls |
| `runtime` | enum | `claude-code`, `codex`, `opencode`, `gemini`, `shell`, `unknown` | Detected agent runtime |
| `state` | enum | `idle`, `running`, `waiting_for_input`, `waiting_for_approval`, `error`, `unknown` | Raw terminal state |
| `availability` | enum | `available`, `busy`, `blocked`, `unknown` | Scheduling signal (derived from state) |
| `working_directory` | string\|null | absolute path | Current working directory |
| `repo` | string\|null | absolute path | Git repo root |
| `branch` | string\|null | | Current git branch |
| `dirty` | bool\|null | | Uncommitted changes present |
| `task_hint` | string\|null | max 120 chars | Most recent user-typed command visible on screen |
| `write_policy` | `"can_submit"` | | Orchestrator is allowed to send and submit text |

## Availability mapping

| state | availability | Meaning |
|---|---|---|
| `idle` | `available` | Ready to accept a new task |
| `active`, `running` | `busy` | Currently processing — don't interrupt |
| `waiting_for_approval` | `blocked` | Paused, needs human or orchestrator action |
| `waiting_for_input` | `blocked` | Waiting for user input |
| `error` | `blocked` | Encountered an error |
| `unknown` | `unknown` | Cannot determine state |

## Example

```json
{
  "agent_id": "w1:t2:p0",
  "runtime": "codex",
  "state": "idle",
  "availability": "available",
  "working_directory": "/Users/me/myapp",
  "repo": "/Users/me/myapp",
  "branch": "feature/payment-retry",
  "dirty": true,
  "task_hint": "implement the retry backoff",
  "write_policy": "can_submit"
}
```
