---
description: Act as a supervisor to orchestrate a complex task across multiple coding agents. Decomposes the task, assigns sub-tasks to available agents, monitors progress, and summarizes results.
---

# Agent Supervisor

Invoke as: `/agent-cockpit:supervisor <task description>`

## Steps

### 1. Discover available agents

Call `list_agents`. If fewer than 1 agent has `availability: available`, report this and stop.

### 2. Decompose the task

Break `$ARGUMENTS` into sub-tasks. Assign each to an agent based on:
- Prefer `availability: available` agents
- Prefer agents in the same repo/branch as the work when relevant
- Prefer `claude-code` for complex reasoning tasks; `codex` for test/verification tasks

### 3. Delegate all sub-tasks

For each assignment:
1. Call `create_handoff_packet` with the sub-task objective and appropriate `requested_action`
2. Call `delegate_to_agent` with `submit: true`

Do not pause between delegations. All assignments go out autonomously.

### 4. Monitor progress

Poll each assigned agent via `inspect_agent` approximately every 10 seconds. Watch for:
- `availability` returning to `available` → agent finished
- `availability: blocked` → note it, continue monitoring others

Give up after 5 minutes and report current status.

### 5. Collect results

When all agents are `available` (or timeout reached), call `inspect_agent` on each.
Read their `screen_tail` to understand what they produced.

### 6. Summarize

Report:
- What each agent completed
- Any errors or blockers encountered
- Recommended next steps
