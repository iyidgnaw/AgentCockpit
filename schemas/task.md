# Task

A Task is an in-session tracking object for orchestration jobs. Not persisted to disk in v0.1.

## Fields

| Field | Type | Description |
|---|---|---|
| `task_id` | string | UUID generated when the skill starts |
| `title` | string | From user description or first sentence of objective |
| `status` | enum | `planning`, `in_progress`, `review`, `done`, `blocked` |
| `owner` | string | `agent_id` of the currently responsible agent |
| `stage` | string | Current phase: `plan`, `implement`, `test`, `review` |
| `handoffs` | list | `[{from_agent_id, to_agent_id, action, timestamp}]` |

## Notes

- Tasks are conversation-scoped — they live in the skill's execution context only
- A single user request typically maps to one Task with multiple handoffs
- Post-MVP (v0.7): persist to `~/.agent-cockpit/tasks/` for cross-session resumability

## Example

```json
{
  "task_id": "550e8400-e29b-41d4-a716-446655440000",
  "title": "Fix payment retry bug",
  "status": "in_progress",
  "owner": "w1:t1:p0",
  "stage": "implement",
  "handoffs": [
    {
      "from_agent_id": "w1:t2:p0",
      "to_agent_id": "w1:t1:p0",
      "action": "continue",
      "timestamp": "2026-07-07T10:30:00Z"
    }
  ]
}
```
