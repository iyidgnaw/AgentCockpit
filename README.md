# AgentCockpit

A Claude Code plugin for orchestrating multiple coding agents across terminal sessions.

## What it does

AgentCockpit lets Claude Code act as an **orchestrator** — discovering agents running in other terminal panes (Codex, another Claude Code, Cursor Agent, etc.), inspecting their state, and delegating tasks to them via structured handoff packets.

## Prerequisites

1. macOS with iTerm2
2. `agent-terminal-mcp` installed at `~/.agent-terminal-mcp`:

```bash
git clone https://github.com/diywang/agent-terminal-mcp ~/.agent-terminal-mcp
cd ~/.agent-terminal-mcp
uv sync
```

3. iTerm2 Python API enabled: **Preferences → General → Magic → Enable Python API**

## Install

```bash
/plugin marketplace add https://github.com/diywang/AgentCockpit
/plugin install agent-cockpit@AgentCockpit
/reload-plugins
```

## Skills

| Skill | Description |
|---|---|
| `/agent-cockpit:inspect` | Show status of all running agents |
| `/agent-cockpit:handoff` | Hand off work from one agent to another |
| `/agent-cockpit:supervisor` | Orchestrate a complex task across multiple agents |
| `/agent-cockpit:debug-swarm` | Send same debug task to all available agents in parallel |

## How it works

Agents running in terminal panes are **workers**. Claude Code is the **orchestrator**. The `agent-terminal-mcp` server gives Claude Code the ability to read any pane, detect what agent is running there, and send it instructions.

Delegation is fully autonomous — no confirmation prompts during execution. Align with the orchestrator upfront on what to do; it handles the rest.
