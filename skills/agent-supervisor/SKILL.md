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

**Choose interval and timeout based on task complexity — do not use fixed values:**

| Task size | Example | Poll interval | Timeout |
|---|---|---|---|
| Trivial | single file edit, config change | 15–30s | 5 min |
| Small | one focused feature, a few files | 30–60s | 15 min |
| Medium | multi-file feature, new module | 60–120s | 30 min |
| Large | new repo, full subsystem | 2–5 min | 60–90 min |
| Very large | multi-repo, parallel heavy builds | 5–10 min | 2–3 hours |

Use judgment — if agents are actively producing output (screen_hash changes each check), lean toward longer intervals. If the task is mostly waiting (CI, build, install), poll less frequently. After timeout, report current status rather than silently giving up.

### 5. Collect results

When all agents are `available` (or timeout reached), call `inspect_agent` on each.
Read their `screen_tail` to understand what they produced.

### 6. Summarize

Report:
- What each agent completed
- Any errors or blockers encountered
- Recommended next steps
