# AgentCockpit Preferences Schema

Stored at: `~/.agent-cockpit/preferences.json`

```json
{
  "backend": "iterm2",
  "allowed_runtimes": ["cursor-agent", "codex"],
  "agent_roles": {
    "cursor-agent": "frontend development and UI work",
    "codex": "code review and test writing"
  }
}
```

## Fields

### `backend`
`"iterm2"` | `"tmux"` — which terminal backend to use.  
Maps to `agent-terminal-mcp` (iterm2) or `agent-tmux-mcp` (tmux) in `.mcp.json`.

### `allowed_runtimes`
Array of runtime keys the user has installed and consents to spawn:
- `"cursor-agent"` — launched with `agent` command
- `"codex"` — launched with `codex` command  
- `"openclaw"` — launched with `openclaw` command
- `"claude-code"` — launched with `claude` command
- `"gemini-cli"` — launched with `gemini` command

If a runtime is not in this list, spawning it requires explicit user confirmation.

### `agent_roles`
Optional map of runtime → role description. Used by the supervisor when decomposing tasks. If absent, supervisor uses its own judgment.

Example role descriptions:
- `"frontend development and UI/UX work"`
- `"backend API and database work"`
- `"code review, testing, and QA"`
- `"architecture and complex reasoning"`
