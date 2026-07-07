---
description: Send the same debug investigation task to all available coding agents in parallel, then synthesize a consensus report from their independent findings.
---

# Agent Debug Swarm

Invoke as: `/agent-cockpit:debug-swarm <bug description>`

## Steps

### 1. Find investigators

Call `list_agents`. Collect all agents with `availability: available`.

If fewer than 2 are available, report: "Need at least 2 available agents for a swarm. Currently available: {count}." Stop.

### 2. Build investigation prompt

```
## Debug Investigation Task

{$ARGUMENTS}

Your role: Investigate independently. Do not fix — only analyze and report.

Report:
1. Root cause hypothesis
2. Evidence supporting it (file:line references if possible)
3. Confidence level (High / Medium / Low)
4. Suggested fix approach (describe, do not implement)
```

### 3. Dispatch to all investigators

For each available agent, call `delegate_to_agent` with:
- `packet`: the investigation prompt above
- `submit`: true

All agents receive identical prompts simultaneously.

### 4. Wait for results

Poll all agents via `inspect_agent` every 10 seconds until all return to `availability: available`, or 5 minutes pass.

### 5. Read findings

Call `inspect_agent` on each agent and read `screen_tail` to get their reports.

### 6. Synthesize consensus report

```markdown
## Debug Swarm Results ({n} investigators)

### Agreed Root Cause
[What all or most agents identified]

### Evidence
[Key evidence cited across investigators]

### Disagreements
[Where agents differed — include which agents held each view]

### Recommended Fix
[The most supported approach]

### Confidence: High / Medium / Low
[Based on agreement level: High = all agree, Medium = majority, Low = split]
```
