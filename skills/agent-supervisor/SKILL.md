---
description: Act as a supervisor to orchestrate a complex task across multiple coding agents. Decomposes the task, assigns sub-tasks to available agents, monitors progress, and summarizes results.
---

# Agent Supervisor

Invoke as: `/agent-cockpit:supervisor <task description>`

## Steps

### 1. Discover available agents

Call `discover_agents` with `workspace` set to the current working directory, then call `list_agents`. If fewer than 1 agent has `availability: available`, report this and stop.

### 2. Decompose the task

Read `~/.agent-cockpit/preferences.json` if it exists. If `agent_roles` is defined, use it to guide assignment (e.g. if `"codex": "code review"`, prefer codex for review sub-tasks).

Break `$ARGUMENTS` into sub-tasks. Assign each to an agent based on:
- **Respect `agent_roles` from preferences** — match task type to agent specialty
- Prefer `availability: available` agents
- Prefer agents in the same repo/branch as the work when relevant
- If no preferences file, use judgment: `claude-code` for complex reasoning, `codex` for test/review tasks

### 3. Delegate all sub-tasks

For each assignment:
1. Call `create_handoff_packet` with the sub-task objective and appropriate `requested_action`
2. Call `delegate_to_agent` with `submit: true`
3. **Immediately call `send_enter` on the same session_id** — `submit: true` pastes the text but some agent runtimes (e.g. Cursor Agent) require a second Enter to actually submit. Always send it.

Do not pause between delegations. All assignments go out autonomously.

### 4. Monitor progress (use /loop)

Use `/loop` to continuously poll until all agents finish. In each loop iteration:
- Call `inspect_agent` on each assigned session
- If `state` is `running` or `unknown` with a changed `screen_hash` → still working, continue
- If `availability` returns to `available` and `screen_hash` is stable → agent finished
- If `availability: blocked` → note it, continue monitoring others

Loop interval: every 30 seconds. Stop loop when all agents are `available` or after 10 minutes total.

### 5. Collect results

When all agents are `available` (or timeout reached), call `inspect_agent` on each.
Read their `screen_tail` to understand what they produced.

### 6. Summarize

Report:
- What each agent completed
- Any errors or blockers encountered
- Recommended next steps
