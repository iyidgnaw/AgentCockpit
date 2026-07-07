# AgentCockpit

A Claude Code plugin for orchestrating multiple coding agents across terminal sessions.

## Install

From within Claude Code:

```
/plugin marketplace add https://github.com/iyidgnaw/AgentCockpit
/plugin install agent-cockpit@AgentCockpit
/reload-plugins
```

## Bootstrap

After installing, run bootstrap to set up your terminal backend and configure `.mcp.json`:

```
/agent-cockpit:bootstrap
```

Bootstrap will:
- Detect your environment (iTerm2 or tmux)
- Install the appropriate MCP server (`agent-terminal-mcp` or `agent-tmux-mcp`)
- Update `.mcp.json` automatically

| Backend | Requires | Best for |
|---|---|---|
| iTerm2 (`agent-terminal-mcp`) | iTerm2 + Python API enabled | macOS desktop |
| tmux (`agent-tmux-mcp`) | tmux in PATH + inside a tmux session | SSH / headless / Linux |

To switch backends later, run `/agent-cockpit:bootstrap` again.

## Usage

**See what agents are running:**
```
/agent-cockpit:inspect
```

**Spawn a new agent to work on a task:**
```
/agent-cockpit:spawn
Spawn a Claude Code agent to implement the auth module
```

**Delegate a complex task across multiple agents:**
```
/agent-cockpit:supervisor
Refactor the API layer: split into router, controller, and service layers, add tests
```

**Debug a hard problem with all agents in parallel:**
```
/agent-cockpit:debug-swarm
The payment webhook fails intermittently — figure out why
```

**Hand off from one agent to another:**
```
/agent-cockpit:handoff
```

## Skills

| Skill | Description |
|---|---|
| `/agent-cockpit:bootstrap` | Set up your terminal backend, install MCP server, configure `.mcp.json` |
| `/agent-cockpit:inspect` | Discover and show status of all agents in the current workspace |
| `/agent-cockpit:spawn` | Spawn one or more new agent sessions in the current workspace |
| `/agent-cockpit:handoff` | Hand off work from the orchestrator to a specific agent |
| `/agent-cockpit:supervisor` | Decompose a task, delegate to available agents, monitor until done, verify results |
| `/agent-cockpit:debug-swarm` | Send the same debug task to all available agents in parallel, synthesize consensus |
