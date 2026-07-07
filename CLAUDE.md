# AgentCockpit Configuration

## Active Backend

The active backend is determined by which `mcpServers` key in `.mcp.json` uses the name `agent-backend` (no leading `_`).

Run `/agent-cockpit:bootstrap` to switch backends — it checks your environment, installs the MCP server if needed, and updates `.mcp.json` automatically.

## Backend Comparison

| | iTerm2 (`agent-terminal-mcp`) | tmux (`agent-tmux-mcp`) |
|---|---|---|
| Requires | iTerm2 + Python API enabled | tmux in PATH + inside a tmux session |
| Sessions | Windows/tabs/panes | tmux panes |
| Best for | macOS desktop | SSH / headless / Linux |

## Manual Switch (advanced)

If you need to switch without running bootstrap:
1. In `.mcp.json`, rename `agent-backend` → `_agent-backend-<old>` and remove `_` prefix from the other entry
2. Run `/reload-plugins` in Claude Code
