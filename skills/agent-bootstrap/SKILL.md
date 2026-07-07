---
description: First-time setup wizard for AgentCockpit. Guides the user through choosing a terminal backend, selecting which agent runtimes to enable, and setting agent role preferences. Saves config to ~/.agent-cockpit/preferences.json.
---

# Agent Bootstrap

Run this once before using other AgentCockpit skills. It creates `~/.agent-cockpit/preferences.json`.

If the file already exists, show its current contents and ask whether to reconfigure.

## Step 1: Choose backend

Ask the user:

> Which terminal backend do you want to use?
> - **iTerm2** (`agent-terminal-mcp`) — macOS desktop, requires iTerm2 with Python API enabled
> - **tmux** (`agent-tmux-mcp`) — works over SSH, headless servers, and Linux

Record their answer as `backend: "iterm2"` or `backend: "tmux"`.

## Step 2: Choose allowed agent runtimes

Ask the user which agent runtimes they have installed and want AgentCockpit to be allowed to spawn automatically:

> Which agent runtimes do you want to enable? (select all that apply)
> - `cursor-agent` — Cursor Agent (launch command: `agent`)
> - `codex` — OpenAI Codex CLI (launch command: `codex`)
> - `openclaw` — OpenClaw (launch command: `openclaw`)
> - `claude-code` — Claude Code (launch command: `claude`)
> - `gemini-cli` — Gemini CLI (launch command: `gemini`)

Record as `allowed_runtimes: [...]`.

**Important:** Any runtime NOT in this list will require explicit user confirmation before being spawned. Remind the user of this rule.

## Step 3: Set agent role preferences (optional)

Ask:

> Do you want to assign specific roles to each agent runtime? This helps the supervisor delegate tasks more intelligently. (optional — press Enter to skip)

If they say yes, for each selected runtime ask:
> What should `<runtime>` specialize in? (e.g. "frontend and UI", "code review and testing", "backend APIs")

Record as `agent_roles: { "<runtime>": "<description>", ... }`.

If skipped, omit `agent_roles` from the file (supervisor will use its own judgment).

## Step 4: Save preferences

Write to `~/.agent-cockpit/preferences.json`:

```json
{
  "backend": "<chosen>",
  "allowed_runtimes": ["<chosen>", ...],
  "agent_roles": {
    "<runtime>": "<role>",
    ...
  }
}
```

Create `~/.agent-cockpit/` directory if it doesn't exist.

## Step 5: Confirm and next steps

Show the saved config and tell the user:

> Setup complete. Preferences saved to `~/.agent-cockpit/preferences.json`.
>
> Next steps:
> - Run `/agent-cockpit:inspect` to discover agents in your workspace
> - Run `/agent-cockpit:spawn` to launch new agent sessions
> - Run `/agent-cockpit:supervisor <task>` to orchestrate a task across your agents
