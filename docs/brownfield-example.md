# Brownfield PIE Example: Adding Alerts to an Existing Stock Screener

This example shows how to use Progressive Intent Engineering (PIE) in an existing codebase. The project already has a working stock screener, and the user wants to add a new capability whose fit is not yet clear.

The key brownfield difference is that PIE must assess both:

- what the new intent means;
- whether the existing system is structurally ready to absorb it.

## Scenario

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

This sounds simple, but it is not delivery-ready. "Alert", "strong candidate", timing, state, notification channel, duplicate handling, persistence, and evaluation semantics are all material questions. The existing screener may also lack the state model needed for alerting.

## 1. Initialize PIE

If PIE has not been initialized:

```text
/pie:init
```

Claude creates or updates:

```text
AGENTS.md
CLAUDE.md
docs/pie/
docs/pie/index.md
```

The index is the durable registry of active intent, active spike, baseline status, blockers, and artifact links.

## 2. Start a Brownfield Intent

Create a named intent:

```text
/pie:intent stock-alerts "Add alerts when a stock becomes a strong screener candidate."
```

Claude should create:

```text
docs/pie/stock-alerts/intent.md
```

For brownfield work, the intent should explicitly include existing-system impact:

```md
---
type: intent
name: stock-alerts
status: discovering
created_at: 2026-05-16
updated_at: 2026-05-16
active: true
delivery_baseline: absent
recommended_next_step: clarify
---

# Intent Brief - Stock Alerts

## Existing Project Intent
The existing project screens a configured ticker list and produces ranked end-of-day candidates for manual review.

## New Intent Signal
Add alerts when a stock becomes a strong screener candidate.

## Potential Impact on the Existing System
- Scoring semantics
- Candidate state over time
- Output model
- Persistence
- Scheduling
- Notification delivery
- Duplicate alert suppression
- Test coverage
```

Claude also registers the intent in:

```text
docs/pie/index.md
```

## 3. Assess Intent Impact

Claude should inspect the existing project before proposing implementation. It should identify where the new intent could affect current purpose and architecture.

Example assessment:

```text
Intent created: stock-alerts
Initial status: needs_clarification

Existing system:
- Computes ranked candidates from daily OHLCV data.
- Writes CSV output for manual review.
- Does not appear to persist prior screener state.
- Does not currently model candidate transitions over time.

Material unknowns:
1. What should trigger an alert: score threshold, rank entry, score improvement, or breakout event?
2. Should alerts be one-time, daily repeated, or state-transition based?
3. What notification channel is required for v1?
4. Does the system need persistence before alerting can work correctly?

Recommended next step:
- Clarify alert semantics and run an integration spike on candidate state persistence.
```

This is the brownfield PIE move: Claude does not just add a notification call. It checks whether the existing system can represent the new behavior coherently.

## 4. Clarify and Auto-Record Settled Decisions

Claude asks material clarification questions:

```text
Decision needed: Alert trigger semantics

This affects scoring, state tracking, duplicate suppression, and tests.

Options:
A. Alert whenever score is above a threshold.
B. Alert when a ticker first crosses into the top candidate tier.
C. Alert only on confirmed breakout.

Recommendation: B.

Reason: It matches the screener's current ranked-candidate purpose and avoids noisy repeated threshold alerts.
```

The user answers:

> Use B. For v1, just write alerts to a local alerts CSV. Do not send email or push notifications yet.

Claude should automatically apply the Convergence Rule. It updates the active intent without requiring `/pie:distill`:

```md
## Decisions Made

### Decision: Alert on first entry into the top candidate tier
- **Decision:** An alert should be created when a ticker first crosses into the top candidate tier.
- **Rationale:** This matches the current ranked-candidate screener and avoids repeated threshold noise.
- **Source:** Clarification response from the user.
- **Impact:** The system needs candidate state across runs and duplicate suppression.
- **Status:** Accepted.

### Decision: Use local CSV alerts for v1
- **Decision:** Version 1 will write alerts to a local CSV.
- **Rationale:** This keeps delivery local and avoids notification-channel complexity.
- **Source:** Clarification response from the user.
- **Impact:** Email, SMS, push, and broker integrations are out of scope.
- **Status:** Accepted.
```

It also updates material unknowns:

```md
## Material Unknowns
- Removed: Which notification channel is required for v1.
- Remaining: Whether the current code has a clean place to persist candidate state.
```

## 5. Create an Integration Spike

The key remaining uncertainty is not product meaning. It is fit with the existing system.

Create a spike:

```text
/pie:spike candidate-state
```

Claude creates:

```text
docs/pie/stock-alerts/spikes/candidate-state/spike.md
```

Example spike:

```md
---
type: spike
name: candidate-state
parent_intent: stock-alerts
status: active
created_at: 2026-05-16
updated_at: 2026-05-16
decision_target: "Can the current screener support transition-based alerts without structural preparation?"
distilled_into_parent: false
---

# Spike - Candidate State

## Question
Can the current screener support alerts when a ticker first enters the top candidate tier?

## Hypothesis
The current CSV-only output path is insufficient because alerting needs persisted previous candidate state.

## Method
Inspect scoring, CLI, and output flow. Prototype a minimal state-store boundary in scratch code or a spike branch.

## Evaluation
Determine whether alerting can be implemented directly or requires prerequisite preparation.

## Decision Impact
Determines whether to create a Preparation Baseline before the alert feature.
```

This spike should stay isolated. If Claude writes exploratory code, it should keep it in scratch space or a disposable branch unless the user explicitly promotes it.

## 6. Distill the Spike

After the spike, run:

```text
/pie:distill
```

Suppose Claude finds:

- the current scorer returns one run's ranked rows only;
- no durable state exists for prior candidate tiers;
- the CLI and output writer are tightly coupled;
- adding alerts directly would mix state tracking into CSV output.

Claude should update the spike and parent intent:

```text
Spike distilled: candidate-state

Key findings:
- Transition-based alerts require persisted previous candidate state.
- The current output module is not a clean place to own alert state.
- Direct feature implementation would likely couple alerting to CSV output.

Recorded decision:
- Add a small candidate-state store boundary before implementing alerts.

Intent status:
- stock-alerts: needs_spike -> ready_for_delivery for preparation work
```

The parent intent now has a prerequisite preparation need.

## 7. Create a Preparation Baseline

Preview or generate the preparation baseline:

```text
/pie:baseline
```

Claude should create:

```text
docs/pie/stock-alerts/preparation-baseline.md
```

Example:

```md
# Preparation Baseline - Candidate State Store

## Purpose
Prepare the existing screener to support transition-based alerts by adding a small candidate-state boundary.

## Current System Constraint
The current screener produces ranked CSV output for a single run and does not persist prior candidate tier state.

## Preparation Scope
- Add a local state-store abstraction for previous candidate tier membership.
- Keep storage local and simple for v1.
- Add tests for detecting first entry into the top candidate tier.
- Keep notification delivery out of this preparation step.

## Out of Scope
- Email, push, SMS, or broker notifications.
- Full alert feature implementation.
- Intraday alerting.

## Success Criteria
- The screener can load prior candidate state.
- The screener can save current candidate state.
- Tests can verify first-entry detection independent of CSV output.

## Sequencing
Must be completed first.

## Trace to Intent
Identified by `docs/pie/stock-alerts/spikes/candidate-state/spike.md`.
```

Preparation is separate from the new alert feature because it makes the system ready to absorb the new intent cleanly.

## 8A. Implement Preparation Directly

If the preparation is small and clear:

```text
/pie:implement
```

Claude should:

1. load the active intent;
2. run the readiness check for preparation work;
3. create or refresh the baseline if needed;
4. mark the intent `in_delivery`;
5. implement the candidate-state store from the preparation baseline.

During direct implementation, Claude may automatically apply feedback behavior if it discovers that preparation is materially different than expected.

## 8B. Export Preparation to [Spec Kit](https://github.com/github/spec-kit) or [LID](https://github.com/jszmajda/lid)

If the team wants a downstream workflow:

```text
/pie:export speckit
```

or:

```text
/pie:export lid
```

Claude writes:

```text
docs/pie/stock-alerts/exports/speckit-seed.md
docs/pie/stock-alerts/exports/lid-seed.md
```

Use [Spec Kit](https://github.com/github/spec-kit) when the team wants a spec -> plan -> tasks -> implementation path.

Use [LID](https://github.com/jszmajda/lid) when the team wants durable HLD/LLD/EARS/test/code traceability for the state-store boundary.

## 9. Return to the Alert Feature

After preparation is complete, Claude should update PIE state:

```md
## Delivery Feedback, if any
- Candidate-state preparation was completed and provides a clean boundary for transition-based alerting.
```

Then the original alert intent can proceed to delivery:

```text
/pie:intent stock-alerts
```

Claude summarizes:

```text
Active intent switched to stock-alerts.

Status:
- ready_for_delivery

Preparation:
- Candidate state store completed.

Delivery Baseline:
- Not yet created for alert feature delivery.

Recommended next step:
- /pie:implement or /pie:export speckit
```

At that point `/pie:implement` or `/pie:export <adapter>` creates or refreshes:

```text
docs/pie/stock-alerts/baseline.md
```

for the actual alert feature.

## 10. Feedback Loop

Suppose downstream [Spec Kit](https://github.com/github/spec-kit) planning discovers:

> The alert trigger cannot be specified without defining whether candidate tier is absolute score-based or percentile-based.

Because this discovery happened in downstream delivery, use explicit feedback:

```text
/pie:feedback "Spec Kit planning showed that candidate tier must be defined as absolute score-based or percentile-based before alert trigger semantics are stable."
```

Claude should:

- classify it as intent-relevant;
- reopen `stock-alerts` if needed;
- add a material unknown;
- recommend a clarification or spike;
- mark the current baseline as needing revision;
- note that the [Spec Kit](https://github.com/github/spec-kit) seed/spec must be patched after PIE is updated.

For direct implementation, Claude may apply the same feedback behavior automatically when it discovers the issue itself.

## 11. Brownfield Workflow Summary

The brownfield PIE rhythm is:

```text
/pie:init
/pie:intent <name> <description>
-> assess existing-system impact
-> auto-record clarification decisions
-> create integration spikes when fit is uncertain
/pie:spike <name>
/pie:distill
-> create preparation baseline if needed
/pie:implement or /pie:export <adapter> for preparation
-> return to the original feature intent
/pie:implement or /pie:export <adapter> for feature delivery
/pie:feedback <description> when downstream delivery changes intent
```

The important distinction is that brownfield PIE separates "make the existing system ready" from "deliver the new intent" when that separation reduces risk or preserves architectural clarity.
