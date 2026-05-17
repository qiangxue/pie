# Brownfield Example: Adding Alerts

This example shows how PIE handles a change to an existing system.

The repository already contains a local stock screener:

```text
package.json
src/
  screener/
    data.ts
    scoring.ts
    cli.ts
    output.ts
tests/
  scoring.test.ts
```

The user says:

> Add alerts so I know when a stock becomes a strong candidate.

This sounds small, but it is not delivery-ready. "Alert", "strong candidate", timing, state, notification channel, duplicate handling, and persistence all affect the design. The current system may not even have the state model alerting needs.

## 1. Initialize PIE

```text
/pie:init
```

For brownfield work, Claude should not invent the Project Goal. It should inspect durable context such as README files, architecture docs, ADRs, product docs, repo structure, and existing agent guidance.

Example:

```text
I found an existing stock screener.

Proposed Project Goal:
Provide a local research tool that ranks end-of-day stock candidates for manual review before potential breakout moves.

Candidate guardrails:
- Preserve ranked candidate semantics.
- Keep scoring explainable enough for manual review.
- Avoid hidden state transitions.
- Keep v1 local and file-based unless a later intent changes that.

Please confirm or revise this Project Goal and guardrails.
```

After confirmation, Claude creates:

```text
docs/pie/project.md
docs/pie/index.md
```

`docs/pie/project.md` might contain:

```md
# PIE Project Context

## Project Goal
Provide a local research tool that ranks end-of-day stock candidates for manual review before potential breakout moves.

## Current System Understanding
- The system scores configured tickers from daily OHLCV data.
- It produces ranked CSV output for manual review.
- It does not appear to persist previous candidate state.

## Project Guardrails
- Preserve ranked candidate semantics.
- Keep scoring explainable enough for manual review.
- Avoid hidden state transitions.
```

`/pie:init` also enforces spike isolation by excluding `spikes/` from Git and excluding both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. `docs/pie/` remains versioned durable state.

## 2. Create The Brownfield Intent

```text
/pie:intent stock-alerts "Add alerts when a stock becomes a strong screener candidate."
```

Claude should assess alignment with the Project Goal:

```text
Project alignment:
- Aligned. Alerts support the research-tool goal by surfacing important candidate transitions.

Potential project impact:
- The current system does not appear to persist candidate state, so alerting may require preparation.
```

The intent is created at:

```text
docs/pie/stock-alerts/intent.md
```

with metadata such as:

```yaml
intent_id: PIE-INTENT-STOCK-ALERTS
project_goal_alignment: aligned
status: discovering
```

Claude should inspect the existing code before proposing implementation.

Example assessment:

```text
Material unknowns:
1. What should trigger an alert: threshold, first entry into a top tier, score improvement, or breakout?
2. Should alerts repeat daily or only fire on state transition?
3. What notification channel is required for v1?
4. Does the system need persistence before alerting can work correctly?

Recommended next step:
- Clarify alert semantics and run an integration spike on candidate state.
```

## 3. Clarify And Converge

Claude asks a material question:

```text
Decision needed: alert trigger semantics

Options:
A. Alert whenever score is above a threshold.
B. Alert when a ticker first enters the top candidate tier.
C. Alert only on confirmed breakout.

Recommendation: B, because it matches ranked-candidate semantics and avoids repeated threshold noise.
```

User:

> Use B. For v1, write alerts to a local alerts CSV. Do not send email or push notifications yet.

Claude should update the intent immediately:

```md
### Decision: Alert on first entry into the top candidate tier
- **Decision:** Create an alert when a ticker first crosses into the top candidate tier.
- **Rationale:** This matches the ranked-candidate purpose and avoids repeated threshold alerts.
- **Source:** User clarification.
- **Impact:** The system needs candidate state across runs and duplicate suppression.
- **Status:** Accepted.

### Decision: Use local CSV alerts for v1
- **Decision:** Version 1 writes alerts to a local CSV.
- **Rationale:** This keeps delivery local and avoids notification-channel complexity.
- **Source:** User clarification.
- **Impact:** Email, SMS, push, and broker integrations are out of scope.
- **Status:** Accepted.
```

No `/pie:distill` is needed for this single clear clarification answer.

## 4. Run A Brownfield Spike

The remaining uncertainty is system fit:

```text
/pie:spike candidate-state
```

Claude creates:

```text
docs/pie/stock-alerts/spikes/candidate-state/spike.md
spikes/candidate-state/
```

The spike asks:

```md
# Spike - Candidate State

## Question
Can the current screener support alerts when a ticker first enters the top candidate tier?

## Hypothesis
The current CSV-only output path is insufficient because alerting needs persisted previous candidate state.

## Decision Impact
Determines whether alerting can be implemented directly or needs prerequisite preparation.
```

If Claude writes exploratory code, it stays in `spikes/candidate-state/`. If the spike must touch real project files, Claude records the paths and disposition in `spike.md`.

## 5. Distill The Spike

```text
/pie:distill
```

Suppose Claude finds:

- the scorer returns one run's rows only;
- no durable prior-state model exists;
- adding alerts directly would mix state tracking into CSV output.

The distillation records:

```text
Recorded decision:
- Add a small candidate-state store boundary before implementing alerts.

Intent status:
- stock-alerts: needs_spike -> ready_for_delivery for preparation work
```

This is the brownfield move: PIE separates "make the system ready" from "deliver the new behavior" when that reduces risk.

## 6. Deliver Preparation

If preparation is small and clear:

```text
/pie:implement
```

If the team wants a downstream workflow:

```text
/pie:export speckit
/pie:export lid
```

PIE creates a baseline revision and delivery ask, for example:

```text
docs/pie/stock-alerts/baselines/PIE-BASELINE-STOCK-ALERTS-R1.md
docs/pie/stock-alerts/asks/PIE-ASK-STOCK-ALERTS-SPECKIT-001.md
```

After preparation is complete, Claude updates PIE state and returns to the alert intent for feature delivery.

## 7. Feed Back Downstream Learning

Suppose [Spec Kit](https://github.com/github/spec-kit) planning discovers:

> The alert trigger cannot be specified without defining whether candidate tier is absolute score-based or percentile-based.

Use feedback:

```text
/pie:feedback "PIE-ASK-STOCK-ALERTS-SPECKIT-002 showed that candidate tier must be defined as absolute score-based or percentile-based before alert trigger semantics are stable."
```

Claude should:

- classify it as intent-relevant;
- reopen `stock-alerts` if needed;
- add a material unknown;
- recommend clarification or a spike;
- mark the current baseline as needing revision;
- note that the downstream seed or spec must be patched after PIE is updated.

Routine implementation details should stay out of PIE.

## Brownfield Rhythm

```text
/pie:init
/pie:project
/pie:intent <name> <description>
-> assess existing-system fit
-> auto-record clarification decisions
/pie:spike <name>
/pie:distill
-> create preparation baseline if needed
/pie:implement or /pie:export <adapter>
/pie:feedback <description> when delivery changes intent
```
