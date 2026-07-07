---
description: First-time setup wizard for AgentCockpit. Checks environment, installs the chosen MCP backend, updates .mcp.json, and saves config to ~/.agent-cockpit/preferences.json.
---

# Agent Bootstrap

Run this once before using other AgentCockpit skills.

If `~/.agent-cockpit/preferences.json` already exists, show its current contents and ask whether to reconfigure.

---

## Step 1: Choose backend

Ask the user:

> Which terminal backend do you want to use?
> - **iTerm2** (`agent-terminal-mcp`) — macOS desktop
> - **tmux** (`agent-tmux-mcp`) — SSH, headless servers, Linux

---

## Step 2: Check environment

### If user chose iTerm2

Run these checks:

```bash
# Check 1: running inside iTerm2
echo $TERM_PROGRAM   # must be "iTerm.app"

# Check 2: Python API is enabled
echo $ITERM2_COOKIE  # must be non-empty
```

**If `TERM_PROGRAM` is not `iTerm.app`:**
> ❌ You're not running inside iTerm2. Please open this terminal session in iTerm2 and re-run `/agent-cockpit:bootstrap`.

**If `ITERM2_COOKIE` is empty:**
> ❌ iTerm2 Python API is not enabled. Enable it:
> 1. Open iTerm2 → **Settings → General → Magic**
> 2. Check **"Enable Python API"**
> 3. Set dropdown to **"Allow all apps to connect"**
> 4. Restart iTerm2, then re-run `/agent-cockpit:bootstrap`.

If both checks pass → continue to Step 3.

---

### If user chose tmux

Run these checks:

```bash
# Check 1: tmux is installed
which tmux

# Check 2: running inside a tmux session
echo $TMUX   # must be non-empty
```

**If `tmux` is not found:**
> ❌ tmux is not installed. Install it:
> - macOS: `brew install tmux`
> - Ubuntu/Debian: `sudo apt install tmux`
> Then re-run `/agent-cockpit:bootstrap`.

**If `TMUX` is empty:**
> ❌ You're not inside a tmux session. Start one first:
> ```bash
> tmux new -s main
> ```
> Then re-run `/agent-cockpit:bootstrap` inside that session.

If both checks pass → continue to Step 3.

---

## Step 3: Install the MCP server

### iTerm2 backend

```bash
git clone https://github.com/iyidgnaw/agent-terminal-mcp ~/.agent-terminal-mcp
cd ~/.agent-terminal-mcp && uv sync
```

If `~/.agent-terminal-mcp` already exists, skip clone and just run `uv sync` (or `git pull && uv sync`).

### tmux backend

```bash
git clone https://github.com/iyidgnaw/agent-tmux-mcp ~/.agent-tmux-mcp
cd ~/.agent-tmux-mcp && uv sync
```

---

## Step 4: Update .mcp.json

Update the `.mcp.json` in the AgentCockpit plugin cache to activate the chosen backend. The file is at:
`~/.claude/plugins/cache/AgentCockpit/agent-cockpit/<version>/.mcp.json`

Find the current version with:
```bash
ls ~/.claude/plugins/cache/AgentCockpit/agent-cockpit/
```

**For iTerm2:** ensure `agent-backend` key points to agent-terminal-mcp and tmux key is prefixed with `_`:

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

**For tmux:** swap — `agent-backend` points to agent-tmux-mcp, iterm key is prefixed with `_`:

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

Write the file directly using the Write tool.

---

## Step 5: Save preferences

Write to `~/.agent-cockpit/preferences.json` (create `~/.agent-cockpit/` if needed):

```json
{
  "backend": "<iterm2 or tmux>",
  "allowed_runtimes": ["<chosen>", ...]
}
```

---

## Step 6: Ask about agent runtimes

Ask the user which runtimes to allow for automatic spawning:

> Which agent runtimes do you want to enable? (select all that apply)
> - `cursor-agent` — launch command: `agent`
> - `codex` — launch command: `codex`
> - `openclaw` — launch command: `openclaw`
> - `claude-code` — launch command: `claude`
> - `gemini-cli` — launch command: `gemini`

Any runtime NOT in this list will require explicit user confirmation before spawning.

Update `preferences.json` with `allowed_runtimes`.

Optionally ask about role preferences (see `schemas/preferences.md`).

---

## Step 7: Confirm

Tell the user:

> ✅ Setup complete.
>
> - Backend: `<chosen>` — MCP installed at `~/.agent-<chosen>-mcp`
> - `.mcp.json` updated
> - Preferences saved to `~/.agent-cockpit/preferences.json`
>
> **Run `/reload-plugins` to activate.**
>
> Next steps:
> - `/agent-cockpit:inspect` — discover agents in your workspace
> - `/agent-cockpit:spawn` — launch new agent sessions
> - `/agent-cockpit:supervisor <task>` — orchestrate tasks across agents
