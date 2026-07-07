---
description: First-time setup wizard for AgentCockpit. Checks environment, installs the chosen MCP backend, updates .mcp.json, and saves config to ~/.agent-cockpit/preferences.json.
---

# Agent Bootstrap

Run this once before using other AgentCockpit skills.

If `~/.agent-cockpit/preferences.json` already exists, show its current contents and ask whether to reconfigure (use AskUserQuestion with Yes/No options).

---

## Step 1: Choose backend

Use **AskUserQuestion** tool with:
- question: "Which terminal backend do you want to use?"
- header: "Backend"
- options:
  - `iTerm2` — agent-terminal-mcp, macOS desktop, requires iTerm2 with Python API enabled
  - `tmux` — agent-tmux-mcp, works over SSH, headless servers, and Linux

---

## Step 2: Check environment

### If user chose iTerm2

Run:
```bash
echo $TERM_PROGRAM
echo $ITERM2_COOKIE
```

**If `TERM_PROGRAM` is not `iTerm.app`:**
> ❌ You're not running inside iTerm2. Open this terminal session in iTerm2 and re-run `/agent-cockpit:bootstrap`.

Stop here.

**If `ITERM2_COOKIE` is empty:**
> ❌ iTerm2 Python API is not enabled. Enable it:
> 1. Open iTerm2 → **Settings → General → Magic**
> 2. Check **"Enable Python API"**
> 3. Set dropdown to **"Allow all apps to connect"**
> 4. Restart iTerm2, then re-run `/agent-cockpit:bootstrap`.

Stop here.

If both pass → continue to Step 3.

---

### If user chose tmux

Run:
```bash
which tmux
```

**If `tmux` is not found:**
> ❌ tmux is not installed.
> - macOS: `brew install tmux`
> - Ubuntu/Debian: `sudo apt install tmux`
> Then re-run `/agent-cockpit:bootstrap`.

Stop here.

Note: The orchestrator (Claude Code) does NOT need to be inside a tmux session. The MCP server manages tmux sessions on behalf of the orchestrator. Checking `$TMUX` is NOT required.

If tmux is in PATH → continue to Step 3.

---

## Step 3: Install the MCP server

### iTerm2 backend

Check if `~/.agent-terminal-mcp` exists. If not:
```bash
git clone https://github.com/iyidgnaw/agent-terminal-mcp ~/.agent-terminal-mcp
```
Then always run:
```bash
cd ~/.agent-terminal-mcp && uv sync
```

### tmux backend

Check if `~/.agent-tmux-mcp` exists. If not:
```bash
git clone https://github.com/iyidgnaw/agent-tmux-mcp ~/.agent-tmux-mcp
```
Then always run:
```bash
cd ~/.agent-tmux-mcp && uv sync
```

---

## Step 4: Update .mcp.json

Find current plugin version:
```bash
ls ~/.claude/plugins/cache/AgentCockpit/agent-cockpit/
```

**Read** the `.mcp.json` file first, then **Write** it with the updated content.

**For iTerm2 — write this exactly:**
```json
{
  "mcpServers": {
    "agent-backend": {
      "type": "stdio",
      "command": "uv",
      "args": ["run", "python", "-m", "agent_terminal_mcp.server"],
      "cwd": "${HOME}/.agent-terminal-mcp"
    },
    "_agent-backend-tmux": {
      "type": "stdio",
      "command": "uv",
      "args": ["run", "python", "-m", "agent_tmux_mcp.server"],
      "cwd": "${HOME}/.agent-tmux-mcp"
    }
  }
}
```

**For tmux — write this exactly:**
```json
{
  "mcpServers": {
    "_agent-backend-iterm": {
      "type": "stdio",
      "command": "uv",
      "args": ["run", "python", "-m", "agent_terminal_mcp.server"],
      "cwd": "${HOME}/.agent-terminal-mcp"
    },
    "agent-backend": {
      "type": "stdio",
      "command": "uv",
      "args": ["run", "python", "-m", "agent_tmux_mcp.server"],
      "cwd": "${HOME}/.agent-tmux-mcp"
    }
  }
}
```

---

## Step 5: Choose allowed runtimes

Use **AskUserQuestion** tool with `multiSelect: true`:
- question: "Which agent runtimes can be spawned automatically (no confirmation needed)? Select all that apply."
- header: "Runtimes"
- options (max 4):
  - `cursor-agent` — launch command: `agent`
  - `codex` — launch command: `codex`
  - `claude-code` — launch command: `claude`
  - `gemini-cli` — launch command: `gemini`

Note: `openclaw` is supported but not listed due to UI limit. If the user mentions it, add it to `allowed_runtimes` manually.

---

## Step 6: Save preferences

Write to `~/.agent-cockpit/preferences.json`:
```json
{
  "backend": "<iterm2 or tmux>",
  "allowed_runtimes": ["<chosen>", ...]
}
```

---

## Step 7: Confirm

Tell the user:

> ✅ Setup complete.
>
> - Backend: `<chosen>` — MCP installed at `~/.agent-<chosen>-mcp`
> - `.mcp.json` updated (active backend: `agent-backend`)
> - Preferences saved to `~/.agent-cockpit/preferences.json`
>
> **Run `/reload-plugins` to activate the new backend.**
>
> Next steps:
> - `/agent-cockpit:inspect` — discover agents in your workspace
> - `/agent-cockpit:spawn` — launch new agent sessions
> - `/agent-cockpit:supervisor <task>` — orchestrate tasks across agents
