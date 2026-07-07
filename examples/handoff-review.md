# Example: Handing Off Work for Code Review

## Scenario

Claude Code has implemented a feature. You want Codex (running in another terminal pane) to review it.

## Steps

1. Run `/agent-cockpit:inspect` — confirm Codex is `available`
2. Run `/agent-cockpit:handoff`
3. When asked:
   - **From**: the Claude Code session that did the implementation
   - **To**: the Codex session
   - **Action**: review
   - **Objective**: "Review the payment retry implementation for correctness and edge cases"

The orchestrator generates and sends the handoff packet automatically. Codex starts reviewing immediately.

## What the receiving agent sees

```markdown
## Objective
Review the payment retry implementation for correctness and edge cases.

## Current State
Agent: claude-code | State: idle | Repo: /Users/me/myapp | Branch: feature/payment-retry
Task hint: implement the retry backoff

## Evidence
### Screen (last 40 lines)
[Claude Code's recent output]

### Git Changes
 src/payment/RetryService.ts | 45 +++++++++++++++++++++++-
 tests/payment/retry.test.ts | 23 +++++++++++
 2 files changed, 68 insertions(+)

## Constraints
None specified

## Requested Action
review

## Completion Criteria
Signal completion by returning to an idle prompt state.
```
