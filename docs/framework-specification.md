# PIE Framework Specification

This is the definitive specification for Progressive Intent Engineering (PIE). For an adoption-oriented overview, read the [README](../README.md).

## 1. Purpose

PIE is a lightweight human-agent workflow for maturing unclear software intent until delivery can proceed without inventing meaning.

PIE is upstream of implementation and delivery frameworks. It prepares better input for code, specs, plans, [Spec Kit](https://github.com/github/spec-kit), [LID](https://github.com/jszmajda/lid), or a team's normal delivery process.

PIE answers:

```text
Is this ready to deliver?
If yes, what stable baseline should delivery use?
If no, what question, decision, evidence, or feedback is missing?
```

## 2. Principles

1. **Do not pretend unclear intent is ready.** If product meaning, scope, architecture, risk, success criteria, or delivery path is materially unclear, stay in discovery.
2. **Clarify only material ambiguity.** Ask when the answer changes product semantics, system boundaries, data model, security, reliability, cost, scale, external contracts, or material scope.
3. **Use evidence when opinion is not enough.** Run a spike when a decision depends on a prototype, code inspection, benchmark, integration check, or domain experiment.
4. **Record learning durably.** Chat is working memory. PIE artifacts are durable memory.
5. **Keep delivery downstream.** PIE does not own task breakdown, implementation plans, or downstream framework internals.
6. **Keep project context above intents.** The Project Goal guides intents but is not an intent and does not enter the discovery -> baseline -> delivery lifecycle.
7. **Feed back only intent-changing learning.** Routine implementation details stay in delivery. Findings that change intent return to PIE.

## 3. Core Model

```text
Project Goal
  -> Intent
    -> Spike
    -> Decision
    -> Delivery Baseline
      -> Delivery Ask
        -> Direct implementation or downstream framework
          -> Feedback, when intent changes
```

PIE should not create recursive intent hierarchies or delivery-unit trees. Large work can be scoped in a baseline, deferred out of scope, or split into peer intents when the goals are genuinely distinct. Delivery decomposition belongs downstream.

## 4. Durable State

PIE state lives under `docs/pie/`. Spike working code lives outside `docs/`.

```text
docs/pie/
  project.md
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
spikes/
  <spike>/
```

`docs/pie/index.md` tracks active context, intent statuses, baseline revisions, delivery asks, downstream targets, child spikes, blockers, and artifact links.

Any command that changes project context, active context, status, readiness, baseline state, delivery ask state, downstream target state, or spike state must update durable artifacts and the index.

## 5. Objects

### Project Context

Location:

```text
docs/pie/project.md
```

Purpose:

- state what the project is fundamentally trying to accomplish;
- provide guardrails for future intents;
- preserve shared principles and brownfield system context where useful.

It is not an intent, spike, baseline, delivery ask, or downstream delivery artifact.

Greenfield shape:

```md
# PIE Project Context

## Project Goal
## Project Guardrails
## Shared Project Principles
## Active Intents
```

Brownfield shape:

```md
# PIE Project Context

## Project Goal
## Current System Understanding
## Project Guardrails
## Known Evolution Themes
## Project-Level Decisions
## Open Project-Level Questions
## Active Intents
```

### Intent

Location:

```text
docs/pie/<intent>/intent.md
```

Purpose:

- capture one specific outcome to clarify and eventually deliver;
- track current understanding, unknowns, evidence, decisions, readiness, and feedback.

Metadata:

```yaml
---
type: intent
intent_id: PIE-INTENT-<INTENT-SLUG>
name: <intent>
project_goal_alignment: aligned|unclear|misaligned
status: discovering
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
active: true
delivery_baseline: absent|current|stale
recommended_next_step: clarify|spike|distill|implement|export|feedback
---
```

`intent_id` is the stable upstream identity of the work. It must not change when the intent matures, delivery repeats, or feedback reopens discovery.

Intent statuses:

| Status | Meaning |
|---|---|
| `discovering` | Material ambiguity remains. |
| `needs_clarification` | Human input is needed. |
| `needs_spike` | Evidence is needed. |
| `ready_for_delivery` | The readiness gate passed. |
| `in_delivery` | Direct implementation or export has started. |
| `reopened` | Feedback exposed new material ambiguity. |
| `completed` | Delivery is complete and no unresolved feedback remains. |

### Spike

Location:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
spikes/<spike>/
```

`spike.md` is the durable record. `spikes/<spike>/` is the working area for experimental code, fixtures, scripts, and notes.

Metadata:

```yaml
---
type: spike
name: <spike>
parent_intent: <intent>
status: proposed|active|completed|distilled|discarded|promoted
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
decision_target: "<decision this spike informs>"
distilled_into_parent: false
---
```

Spike-only work must be isolated from production code unless explicitly promoted. `/pie:init` must exclude `spikes/` from Git, and must exclude both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. `docs/pie/` is durable project state and should normally stay versioned.

### Decision

A decision is a settled intent-level choice.

Record shape:

```md
### Decision: Short title
- **Decision:** The accepted decision.
- **Rationale:** Why this decision is appropriate.
- **Source:** Clarification, spike, external meeting, accepted recommendation, or override.
- **Impact:** What changes because of this decision.
- **Status:** Accepted, rejected, overridden, or deferred.
```

Clarification answers and settled conversation conclusions should be recorded automatically when they resolve material ambiguity. `/pie:decision` is for manual recording, affirmation, or override.

### Delivery Baseline

Current baseline:

```text
docs/pie/<intent>/baseline.md
```

Immutable revision snapshots:

```text
docs/pie/<intent>/baselines/<baseline_id>.md
```

Metadata:

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

Baseline sections:

```md
# Delivery Baseline - <Intent Title>

## Goal
## Context
## In Scope
## Out of Scope
## Decisions
## Constraints
## Success Criteria
## Assumptions
## Deferred Questions
## Recommended Delivery Path
## Trace to PIE Discovery
```

`baseline.md` is the latest readable copy. Delivery asks must reference immutable baseline revisions.

### Preparation Baseline

Location:

```text
docs/pie/<intent>/preparation-baseline.md
```

Use this only when a brownfield system needs prerequisite structural work before the new intent can be delivered cleanly.

### Delivery Ask

Location:

```text
docs/pie/<intent>/asks/<ask_id>.md
```

Purpose:

- record a handoff from PIE to direct implementation or a downstream framework;
- preserve the exact intent and baseline revision used for delivery.

Metadata:

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

If the downstream target is unknown during export, record a pending or proposed target and update the ask when known.

## 6. Readiness Gate

An intent is ready for delivery only when:

- the goal is clear enough to implement;
- the intent aligns with the Project Goal or project drift has been explicitly resolved;
- relevant project guardrails are reflected;
- material decisions are made or explicitly deferred as non-blocking;
- constraints and non-negotiables are known;
- success criteria are understandable;
- no active or undistilled spike blocks delivery;
- delivery can proceed without silently inventing intent;
- for brownfield work, prerequisite preparation has been assessed and separated where needed.

If any item fails, remain in discovery, ask a material question, create or continue a spike, or distill accumulated learning.

## 7. Command Semantics

| Command | Purpose |
|---|---|
| `/pie:init` | Initialize project context, index, agent guidance, and spike isolation. |
| `/pie:project` | Display or update Project Goal, guardrails, principles, and brownfield context. |
| `/pie:intent <name> <description>` | Create an intent, assess project alignment, and assess readiness. |
| `/pie:intent [name]` | List intents or switch active intent. |
| `/pie:spike [name]` | List, create, inspect, select, or continue a spike. |
| `/pie:distill` | Fold spike findings or long discovery context into durable intent updates. |
| `/pie:decision <description>` | Manually record, affirm, reject, or override a decision. |
| `/pie:implement` | Run readiness gate, create baseline revision and ask, then implement directly. |
| `/pie:export <adapter>` | Run readiness gate, create baseline revision and ask, then write downstream seed. |
| `/pie:feedback <description>` | Reconcile intent-changing delivery feedback back into PIE. |
| `/pie:baseline` | Optional preview or explicit baseline generation without delivery. |

## 8. Normal Flow

1. `/pie:init` creates project context and durable state.
2. `/pie:intent <name> <description>` creates an intent under the Project Goal.
3. The agent clarifies material ambiguity. Clear answers update the intent immediately.
4. `/pie:spike <name>` is used when evidence is needed.
5. `/pie:distill` folds spike findings or long discovery into the intent.
6. When the readiness gate passes, `/pie:implement` or `/pie:export <adapter>` creates a baseline revision and delivery ask.
7. `/pie:feedback` reopens or updates PIE only when delivery changes intent.

## 9. Traceability

Every delivery handoff must preserve this chain:

```text
PIE Intent ID
  -> Delivery Baseline ID
    -> Delivery Ask ID
      -> Downstream Target ID
```

The intent and index should retain delivery history. Downstream seeds should include:

```md
## PIE Origin
- PIE Intent: `PIE-INTENT-<INTENT-SLUG>`
- PIE Delivery Baseline: `PIE-BASELINE-<INTENT-SLUG>-R<N>`
- PIE Delivery Ask: `PIE-ASK-<INTENT-SLUG>-<TARGET>-<NNN>`
```

Repeated exports of the same intent to the same adapter should default to updating the known downstream target while creating a new ask record for the new handoff. Create a new downstream target only when the user requests it, the work is materially independent, or the downstream framework requires it.

Feedback should record ask lineage when available:

```yaml
feedback_source:
  ask_id: PIE-ASK-<INTENT-SLUG>-<TARGET>-<NNN>
  baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
  downstream_target_id: <target-id>
  target_framework: direct|speckit|lid
```

## 10. Adapter Rule

Adapters produce PIE-owned downstream seeds. They do not replace downstream workflows.

Current adapters:

- [`speckit`](https://github.com/github/spec-kit): writes `docs/pie/<intent>/exports/speckit-seed.md`.
- [`lid`](https://github.com/jszmajda/lid): writes `docs/pie/<intent>/exports/lid-seed.md`.

If export reveals missing delivery-critical intent, stop and reopen PIE instead of inventing downstream requirements.
