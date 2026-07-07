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

**Supervisor is the main entry point.** Describe your task and it handles everything — spawning agents if needed, decomposing the work, delegating, monitoring, and reporting results.

```
/agent-cockpit:supervisor
Refactor the API layer: split into router, controller, and service layers, add tests
```

Supervisor reads `~/.agent-cockpit/preferences.json` to know which agents to use and what each one is responsible for. If the required agents aren't running, it spawns them automatically before delegating work.

```json
{
  "allowed_runtimes": ["codex", "cursor-agent"],
  "agent_roles": {
    "codex": "write implementation code",
    "cursor-agent": "code review and QA"
  }
}
```

With this config, supervisor will spin up both agents if needed, then route coding tasks to codex and review tasks to cursor-agent — no manual setup required.

The other skills are available for manual use when you need finer control:

```
/agent-cockpit:inspect          # see what agents are running
/agent-cockpit:spawn            # manually spawn an agent
/agent-cockpit:handoff          # hand off a task to a specific agent
/agent-cockpit:debug-swarm      # send the same debug task to all agents in parallel
```

## Skills

| Skill | Description |
|---|---|
| `/agent-cockpit:bootstrap` | Set up your terminal backend, install MCP server, configure `.mcp.json` |
| `/agent-cockpit:supervisor` | **Main entry point.** Decompose a task, auto-provision agents if needed, delegate, monitor, and summarize |
| `/agent-cockpit:inspect` | Discover and show status of all agents in the current workspace |
| `/agent-cockpit:spawn` | Manually spawn one or more new agent sessions |
| `/agent-cockpit:handoff` | Hand off work from the orchestrator to a specific agent |
| `/agent-cockpit:debug-swarm` | Send the same debug task to all available agents in parallel, synthesize consensus |
