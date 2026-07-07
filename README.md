# AgentCockpit

A Claude Code plugin for orchestrating multiple coding agents across terminal sessions.

## What it does

AgentCockpit lets Claude Code act as an **orchestrator** — spawning agents in new terminal panes, discovering what's already running, and delegating tasks to them autonomously via structured handoff packets.

## Prerequisites

1. macOS with iTerm2
2. iTerm2 Python API enabled: **iTerm2 → Preferences → General → Magic → Enable Python API**
3. `agent-terminal-mcp` installed and configured in Claude Code's MCP settings

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
