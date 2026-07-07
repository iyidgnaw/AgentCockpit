---
description: Hand off work from one coding agent to another. Creates a structured handoff packet from the source agent's current state and sends it to the target agent autonomously.
---

# Agent Handoff

Invoke as: `/agent-cockpit:handoff [from:<session_id> to:<session_id> action:<action>]`

## Steps

### 1. Identify from/to/action

If `$ARGUMENTS` contains `from:`, `to:`, and `action:`, parse them directly.

Otherwise:
- Call `list_agents` to show available agents
- Ask the user: which agent is handing off work, which agent should receive it, and what should it do (review / continue / test / debug / summarize)

### 2. Get objective

If not provided in arguments, ask: "What should the receiving agent accomplish?" (one sentence)

### 3. Create the handoff packet

Call `create_handoff_packet` with:
- `from_session_id`: source agent's session_id
- `objective`: the one-sentence goal
- `requested_action`: review | continue | test | debug | summarize
- `constraints`: omit unless user specified any

### 4. Delegate

Call `delegate_to_agent` with:
- `to_session_id`: target agent's session_id
- `packet`: the packet from step 3
- `submit`: true

**Do not ask the user to confirm before sending.** Execution is fully autonomous once from/to/action are set.

### 5. Report

Reply: "Handed off to `{to_session_id}` ({runtime}). Packet sent ({char_count} chars). Action: {requested_action}."
