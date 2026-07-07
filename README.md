# AgentCockpit

A Claude Code plugin for orchestrating multiple coding agents across terminal sessions.

## What it does

AgentCockpit lets Claude Code act as an **orchestrator** — spawning agents in new terminal panes, discovering what's already running, and delegating tasks to them autonomously via structured handoff packets.

## Prerequisites

1. A terminal backend (see [Backend Configuration](#backend-configuration))
2. The corresponding MCP server installed (`agent-terminal-mcp` or `agent-tmux-mcp`)

## Backend Configuration

AgentCockpit talks to terminal sessions through an MCP backend. Both backends expose the same tool names (`list_sessions`, `send_text`, etc.), so skills work unchanged — only `.mcp.json` needs to be updated.

| | iTerm2 (`agent-terminal-mcp`) | tmux (`agent-tmux-mcp`) |
|---|---|---|
| Requires | iTerm2 + Python API enabled | tmux in PATH |
| Sessions | Windows/tabs/panes | tmux panes |
| Best for | macOS desktop | SSH / headless / Linux |
| Install location | `~/.agent-terminal-mcp` | `~/.agent-tmux-mcp` |

**Default:** iTerm2 (`agent-backend` key in `.mcp.json` points to `agent-terminal-mcp`).

### Switch to tmux

1. In `.mcp.json`, rename `agent-backend` → `_agent-backend-iterm` and `_agent-backend-tmux` → `agent-backend`
2. Install `agent-tmux-mcp` at `~/.agent-tmux-mcp`
3. Run `/reload-plugins` in Claude Code

### Switch back to iTerm2

Reverse the key rename: `_agent-backend-iterm` → `agent-backend` and `agent-backend` → `_agent-backend-tmux`, then `/reload-plugins`.

See `CLAUDE.md` for a quick reference.

## Install

From within Claude Code:

```
/plugin marketplace add https://github.com/iyidgnaw/AgentCockpit
/plugin install agent-cockpit@AgentCockpit
/reload-plugins
```

## Skills

| Skill | Description |
|---|---|
| `/agent-cockpit:inspect` | Discover and show status of all agents in the current workspace |
| `/agent-cockpit:spawn` | Spawn one or more new agent sessions in the current workspace |
| `/agent-cockpit:handoff` | Hand off work from the orchestrator to a specific agent |
| `/agent-cockpit:supervisor` | Decompose a task, delegate to available agents, monitor until done, verify results |
| `/agent-cockpit:debug-swarm` | Send the same debug task to all available agents in parallel, synthesize consensus |

## How it works

**Workers** are agents running in iTerm2 panes. **Orchestrator** is Claude Code with this plugin.

1. `/agent-cockpit:inspect` — scans all panes in your current workspace, sends a "who are you" handshake to idle sessions, builds a registry of known agents
2. `/agent-cockpit:spawn` — creates new iTerm2 tabs and launches agents (`agent` for Cursor Agent, `claude` for Claude Code, `codex` for Codex)
3. `/agent-cockpit:supervisor` — decomposes your task, delegates sub-tasks autonomously, monitors every ~10s, collects and verifies results

Workers need zero setup — they just need to be running in an iTerm2 pane in the same working directory as the orchestrator.

Delegation is fully autonomous — no confirmation prompts. Align with the orchestrator upfront; it handles the rest.
