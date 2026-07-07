# Example: Parallel Debug with Multiple Agents

## Scenario

A test is failing and you want multiple agents to investigate independently and simultaneously.

## Steps

Run:
```
/agent-cockpit:debug-swarm The checkout test fails with "Payment timeout after 3 retries". 
Error: RetryService threw TimeoutError at retry 3. 
Test: tests/payment/checkout.test.ts:142.
```

All available agents receive the same investigation prompt. They work independently.

## Example consensus output

```markdown
## Debug Swarm Results (3 investigators)

### Agreed Root Cause
RetryConfig.maxDelay defaults to 5000ms but the test mock uses 100ms,
causing a timing mismatch that trips the timeout on retry 3.

### Evidence
- Claude Code: found default in src/payment/RetryConfig.ts:42
- Codex: traced mock in tests/mocks/payment.ts:18
- Gemini: confirmed via stack trace in test failure log

### Disagreements
None — all 3 investigators reached the same conclusion.

### Recommended Fix
Override maxDelay in the test mock to match production defaults,
or parameterize RetryConfig to accept test-friendly timing values.

### Confidence: High (3/3 agreement)
```
