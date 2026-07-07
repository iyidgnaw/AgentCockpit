# AgentCockpit Configuration

## Active Backend
**Current backend:** iTerm2 (`agent-terminal-mcp`)

To switch to tmux backend:
1. In `.mcp.json`, rename `agent-backend` → `_agent-backend-iterm` and `_agent-backend-tmux` → `agent-backend`
2. Ensure `agent-tmux-mcp` is installed at `~/.agent-tmux-mcp`
3. Run `/reload-plugins` in Claude Code

## Backend Comparison
| | iTerm2 (`agent-terminal-mcp`) | tmux (`agent-tmux-mcp`) |
|---|---|---|
| Requires | iTerm2 + Python API enabled | tmux in PATH |
| Sessions | Windows/tabs/panes | tmux panes |
| Best for | macOS desktop | SSH / headless / Linux |
