# PIE Framework Specification

This document is the definitive framework specification for Progressive Intent Engineering (PIE). For an adoption-oriented overview, read the [README](../README.md).

## 1. Purpose

PIE is a lightweight human-agent workflow for turning uncertain software intent into delivery-ready intent.

PIE is upstream of delivery. It does not replace implementation plans, specs, tests, design docs, [Spec Kit](https://github.com/github/spec-kit), [LID](https://github.com/jszmajda/lid), or any organization-specific delivery process. It prepares better input for them.

PIE answers:

```text
Is this work ready to deliver?
If yes, what stable intent should delivery begin from?
If no, what uncertainty must be resolved first?
```

## 2. Principles

### 2.1 Do not pretend unclear intent is ready

If the goal, product semantics, architecture, data model, success criteria, risk, or delivery path is materially unclear, enter Discover instead of implementation.

### 2.2 Clarify only material ambiguity

Ask questions when the answer changes:

- product semantics;
- architecture or system boundaries;
- security, privacy, or compliance;
- operability or reliability;
- cost, performance, or scale;
- external contracts;
- material scope.

For local, reversible decisions, the agent should make a reasonable choice and proceed.

### 2.3 Preserve learning durably

Chat history is working memory. PIE artifacts are durable memory.

Material decisions, findings, spikes, baselines, and feedback must be captured in files.

### 2.4 Use evidence when discussion is not enough

If a decision depends on empirical evidence, run a spike.

### 2.5 Keep delivery downstream

PIE produces a Delivery Baseline or downstream seed. Delivery frameworks own specs, plans, tasks, implementation, tests, and code.

## 3. Core Objects

### 3.1 Intent

An intent is the durable representation of a project direction or deliverable that may still be evolving.

An intent normally lives at:

```text
docs/pie/<intent>/intent.md
```

Intent metadata:

```yaml
---
type: intent
intent_id: PIE-INTENT-<INTENT-SLUG>
name: <intent>
status: discovering
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
active: true
delivery_baseline: absent
recommended_next_step: clarify
---
```

`intent_id` is the stable upstream identity for the work. It must not change when the intent matures, when delivery happens in multiple iterations, or when feedback reopens discovery.

Recommended intent statuses:

| Status | Meaning |
|---|---|
| `discovering` | Material ambiguity remains. |
| `needs_clarification` | Waiting for human answers to important questions. |
| `needs_spike` | Empirical uncertainty should be resolved. |
| `ready_for_delivery` | No blocking ambiguity remains. |
| `in_delivery` | `/pie:implement` or `/pie:export` has started delivery. |
| `reopened` | Delivery feedback exposed new material ambiguity. |
| `completed` | Delivery is done and no unresolved feedback remains. |

### 3.2 Spike

A spike is a focused empirical investigation created to resolve a named uncertainty within an intent.

It normally lives at:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
```

Spike-only code, fixtures, notes, or scripts should live outside `docs/` under:

```text
spikes/<spike>/
```

The `docs/pie/.../spike.md` file is the durable record. The top-level `spikes/<spike>/` directory is the working area for experimental code. If the spike must touch the real application to gather evidence, the agent may edit normal project files, but it must record the touched paths in `spike.md`. Spike code should not become production code unless it is intentionally promoted during delivery.

PIE initialization must enforce this separation in repository tooling. At minimum, `spikes/` must be excluded from Git tracking. When applicable, `spikes/` and `docs/pie/` must also be excluded from lint, test, build, and package-publish inputs unless the user explicitly promotes those artifacts.

Spike metadata:

```yaml
---
type: spike
name: <spike>
parent_intent: <intent>
status: active
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
decision_target: "<decision this spike informs>"
distilled_into_parent: false
---
```

Recommended spike statuses:

| Status | Meaning |
|---|---|
| `proposed` | Suggested but not yet started. |
| `active` | Currently being worked on. |
| `completed` | Work finished, findings not yet distilled. |
| `distilled` | Findings incorporated into the parent intent. |
| `discarded` | Abandoned or determined not useful. |
| `promoted` | Findings became formal design or delivery input. |

### 3.3 Decision

A decision is a settled intent-level choice.

Decision record shape:

```md
### Decision: Short title
- **Decision:** The accepted decision.
- **Rationale:** Why this decision is appropriate.
- **Source:** Clarification, spike, external meeting, accepted recommendation, or override.
- **Impact:** What changes because of this decision.
- **Status:** Accepted, rejected, overridden, or deferred.
```

### 3.4 Delivery Baseline

A Delivery Baseline is the delivery-facing summary of stable intent.

The latest readable baseline normally lives at:

```text
docs/pie/<intent>/baseline.md
```

Each delivery handoff must also create an immutable baseline revision snapshot:

```text
docs/pie/<intent>/baselines/<baseline_id>.md
```

Baseline metadata:

```yaml
---
type: delivery_baseline
baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
source_intent_id: PIE-INTENT-<INTENT-SLUG>
revision: <N>
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
status: current
---
```

`baseline.md` is the current convenience copy. Ask records must point to the immutable revision snapshot that was used for delivery.

Baseline sections:

```md
# Delivery Baseline - <Delivery Unit>

## Goal
## Context
## In-Scope Intent
## Out of Scope
## Clarified Decisions
## Constraints and Non-Negotiables
## Success Criteria
## Explicit Assumptions
## Deferred Questions
## Recommended Delivery Path
## Trace to PIE Discovery
```

### 3.5 Preparation Baseline

A Preparation Baseline is used when an existing system needs structural work before the new intent can be delivered cleanly.

It normally lives at:

```text
docs/pie/<intent>/preparation-baseline.md
```

### 3.6 Delivery Ask

A Delivery Ask is a durable record of a handoff from PIE to direct implementation or a downstream delivery framework.

It normally lives at:

```text
docs/pie/<intent>/asks/<ask_id>.md
```

Ask metadata:

```yaml
---
type: delivery_ask
ask_id: PIE-ASK-<INTENT-SLUG>-<TARGET>-<NNN>
source_intent_id: PIE-INTENT-<INTENT-SLUG>
source_baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
delivery_mode: direct|export
target_framework: direct|speckit|lid
created_at: YYYY-MM-DDTHH:mm:ss<offset>
downstream_target:
  framework: direct|speckit|lid
  target_id: <known-or-proposed-target-id>
  target_kind: implementation_session|new_feature_seed|existing_feature_update|new_scope_seed|existing_scope_update|unknown
  status: pending|proposed|known|updated
---
```

The ask record answers which intent produced the request, which baseline revision was used, where the work went, and when the handoff happened.

If the downstream target ID is not known at export time, the ask may record a proposed or pending target. The ask, index, and export history should be updated when the downstream target becomes known.

### 3.7 Traceability Chain

Every delivery handoff must preserve this chain:

```text
PIE Intent ID
  -> Delivery Baseline ID
    -> Delivery Ask ID
      -> Downstream Target ID
```

PIE does not model downstream task decomposition. It only preserves lineage between what was meant, what was handed off, where it went, and what came back.

## 4. Durable State

PIE maintains an index:

```text
docs/pie/index.md
```

The index tracks:

- active intent;
- active spike;
- intent statuses;
- baseline status;
- baseline revisions;
- delivery ask history;
- downstream target IDs when known;
- child spikes;
- major blockers;
- artifact links.

Every command that changes active context, status, baseline state, delivery ask state, downstream target state, or spike state must update the index.

Recommended durable state layout:

```text
docs/pie/
  index.md
  <intent>/
    intent.md
    baseline.md
    baselines/
      <baseline_id>.md
    asks/
      <ask_id>.md
    exports/
    preparation-baseline.md
    spikes/
      <spike>/
        spike.md
```

## 5. Workflow

### 5.1 Start or select intent

Create:

```text
/pie:intent <name> <description>
```

List or switch:

```text
/pie:intent
/pie:intent <name>
```

### 5.2 Clarify and converge

When the agent asks a material clarifying question and the user answers, the agent should immediately determine whether the answer:

1. resolves a tracked ambiguity;
2. creates or confirms a decision;
3. changes current understanding;
4. affects readiness.

If yes, the agent updates the active intent and index automatically.

### 5.3 Spike when evidence is needed

Create or select:

```text
/pie:spike <name>
```

Distill when findings are ready:

```text
/pie:distill
```

Distillation:

- summarizes findings;
- updates the spike artifact;
- records settled decisions;
- recommends unresolved decisions;
- updates the parent intent;
- updates readiness and index state.

### 5.4 Manually record decisions when needed

Use:

```text
/pie:decision <description>
```

for explicit recording, affirming, rejecting, or overriding decisions. It is not required after every clarification or distillation.

### 5.5 Deliver when ready

Delivery may begin only when:

- the goal is clear enough to implement;
- material decisions are made or explicitly deferred as non-blocking;
- constraints are known;
- success criteria are understandable;
- delivery can proceed without inventing intent;
- for existing systems, prerequisite preparation has been assessed.

Direct implementation:

```text
/pie:implement
```

Export:

```text
/pie:export speckit
/pie:export lid
```

Both commands create or refresh the Delivery Baseline after readiness passes.

Each delivery command must also:

- create a new immutable baseline revision if the delivery input changed;
- create a Delivery Ask record;
- update the intent and index export history;
- include PIE origin metadata in downstream seed artifacts.

When exporting the same intent to the same downstream framework again, PIE should default to updating the existing downstream target when one is known. It should still create a new ask record for the new handoff. A new downstream target should be created only when the user requests it, the work is materially independent, or the downstream framework requires it.

### 5.6 Reconcile feedback

Use:

```text
/pie:feedback <description>
```

when implementation, downstream planning, production, or user feedback changes intent.

Routine implementation details should not churn PIE artifacts.

Feedback should reference the delivery ask lineage when available:

```yaml
feedback_source:
  ask_id: PIE-ASK-<INTENT-SLUG>-<TARGET>-<NNN>
  baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
  downstream_target_id: <target-id>
  target_framework: direct|speckit|lid
```

This lets PIE identify which intent to reopen, which baseline assumption was challenged, and whether a downstream seed or target must be regenerated or patched.

## 6. Brownfield Rule

For existing systems, PIE must assess:

- how the new intent affects the current project intent;
- whether architecture, data model, APIs, state model, tests, or operations are ready;
- whether preparation should be separated from the new feature.

If preparation is needed, create a Preparation Baseline before or alongside the new-intent Delivery Baseline.

## 7. Adapter Rule

PIE adapters produce downstream seeds. They do not replace downstream workflows.

Current adapters:

- [`speckit`](https://github.com/github/spec-kit): writes `docs/pie/<intent>/exports/speckit-seed.md`.
- [`lid`](https://github.com/jszmajda/lid): writes `docs/pie/<intent>/exports/lid-seed.md`.

If export reveals missing delivery-critical intent, stop and reopen PIE instead of inventing downstream requirements.

Adapter outputs must include a bidirectional provenance block:

```md
## PIE Origin
- PIE Intent: `PIE-INTENT-<INTENT-SLUG>`
- PIE Delivery Baseline: `PIE-BASELINE-<INTENT-SLUG>-R<N>`
- PIE Delivery Ask: `PIE-ASK-<INTENT-SLUG>-<TARGET>-<NNN>`
```
