# PIE Framework Specification

This is the definitive specification for Progressive Intent Engineering (PIE). For an adoption-oriented overview, read the [README](../README.md).

## 1. Purpose

PIE is a lightweight human-agent workflow for maturing unclear software intent until delivery can proceed without inventing meaning.

PIE sits upstream of implementation and delivery frameworks. It prepares better input for code, specs, plans, [Spec Kit](https://github.com/github/spec-kit), [LID](https://github.com/jszmajda/lid), or a team's normal delivery process.

PIE answers:

```text
Is this ready to deliver?
If yes, what stable baseline should delivery use?
If no, what question, decision, evidence, or feedback is missing?
```

## 2. Principles

1. **Do not pretend unclear intent is ready.** If product meaning, scope, architecture, risk, success criteria, or delivery path is materially unclear, stay in discovery.
2. **Clarify only material ambiguity.** An ambiguity is material when two plausible answers would produce a meaningfully different Delivery Baseline.
3. **Use evidence when opinion is not enough.** Run a spike when a decision depends on a prototype, code inspection, benchmark, integration check, or domain experiment.
4. **Record learning durably.** Chat is working memory. PIE artifacts are durable memory.
5. **Keep delivery downstream.** PIE does not own task breakdown, implementation plans, or downstream framework internals.
6. **Keep project context above intents.** The Project Goal guides intents but is not an intent and does not enter the discovery -> baseline -> delivery lifecycle.
7. **Feed back only intent-changing learning.** Routine delivery details stay in delivery. Findings that change intent return to PIE.
8. **Make artifacts authoritative.** `project.md`, `intent.md`, `spike.md`, baseline files, ask records, and export seeds are the source of truth. `index.md` is derived and repairable.

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
    spikes/
      <spike>/
        spike.md
spikes/
  <spike>/
```

### Authoritative Files

These files are authoritative:

- `docs/pie/project.md`
- `docs/pie/<intent>/intent.md`
- `docs/pie/<intent>/spikes/<spike>/spike.md`
- `docs/pie/<intent>/baseline.md`
- `docs/pie/<intent>/baselines/<baseline_id>.md`
- `docs/pie/<intent>/asks/<ask_id>.md`
- export seed files under `docs/pie/<intent>/exports/`

`docs/pie/index.md` is a derived registry. It summarizes artifacts, statuses, baseline revisions, delivery asks, downstream targets, child spikes, blockers, and links. It must not be treated as co-equal source of truth.

If the index conflicts with authoritative artifacts, the artifacts win and the index should be regenerated or repaired. State-touching commands should reconcile the index automatically when they notice drift.

### Session Context

Active intent and active spike are session-local concepts. They must not be stored as a shared durable cursor in `docs/pie/index.md`.

Commands may use the current session selection after a user runs:

```text
/pie:intent <name>
/pie:spike <name>
```

If session context is unclear, the command should ask for the intent or spike name, or accept it explicitly in the command. If an implementation later persists session context, it should use local ignored state, not `docs/pie/`.

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

It is not an intent, spike, baseline, delivery ask, or downstream delivery artifact. It should not track active intents, intent status, or spike status.

Greenfield shape:

```md
# PIE Project Context

## Project Goal
## Project Guardrails
## Shared Project Principles
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
```

### Intent

Location:

```text
docs/pie/<intent>/intent.md
```

Purpose:

- capture one specific outcome to clarify and eventually deliver;
- track current understanding, material unknowns, evidence, decisions, readiness, delivery history, and feedback.

Metadata:

```yaml
---
type: intent
intent_id: PIE-INTENT-<INTENT-SLUG>
name: <intent>
project_goal_alignment: aligned|unclear|misaligned
status: discovering
readiness: not_ready|borderline|ready
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
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

### Material Ambiguity

An ambiguity is material if two plausible answers would produce a meaningfully different Delivery Baseline.

Apply this test:

Would a different answer change the goal, in-scope behavior, success criteria, non-negotiable constraints, delivery readiness, or downstream ask?

If yes, it is material. If no, do not escalate it into PIE.

Examples of material ambiguity:

- binary output versus ranked candidates;
- daily-only data versus intraday data;
- recommend actions versus execute actions;
- tenant-specific service versus generalized platform.

Examples of non-material ambiguity:

- local helper function names;
- temporary spike output format;
- temporary logging level;
- reversible implementation details that do not change the baseline.

### Convergence Rule

Convergence turns clarification into durable intent updates.

| Case | Rule |
|---|---|
| Explicit answer or accepted option | Auto-record the decision, remove the ambiguity, and refresh readiness. |
| Explicit user decision | Auto-record the decision and refresh readiness. |
| Accepted pending recommendation | Auto-record the decision and refresh readiness. |
| Inferred decision or project-framing shift | Propose the decision and ask for confirmation before recording as accepted. |
| Partial answer | Update understanding, keep unresolved material ambiguity visible. |
| Exploratory remark or non-material detail | Do not update PIE artifacts. |

`/pie:distill` is not required after ordinary one-question clarification. It is for spike findings, long or branched conversations, accumulated evidence, and explicit checkpoints.

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
status: proposed|active|completed|distilled|abandoned
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
decision_target: "<decision this spike informs>"
distilled_into_parent: false
---
```

Spike statuses:

| Status | Meaning |
|---|---|
| `proposed` | Suggested but not started. |
| `active` | Currently being investigated. |
| `completed` | Work is done, not yet distilled. |
| `distilled` | Findings have been incorporated into the parent intent. |
| `abandoned` | Stopped without useful conclusion. |

Spike code is exploratory by default and should not be treated as production code merely because it exists. Downstream delivery may reuse, rewrite, or discard it according to ordinary engineering judgment.

`/pie:init` must exclude `spikes/` from Git, and must exclude both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. `docs/pie/` is durable project state and should normally stay versioned.

### Decision

A decision is an intent-level choice with rationale and impact.

Record shape:

```md
### Decision: Short title
- **Decision ID:** DEC-<INTENT-SLUG>-001
- **Decision:** The accepted or proposed decision.
- **Rationale:** Why this decision is appropriate.
- **Source:** clarification_response, spike_finding, explicit_user_decision, delivery_feedback, or external_input.
- **Impact:** What changes because of this decision.
- **Status:** proposed, accepted, rejected, or superseded.
```

Clarification answers and settled conversation conclusions should be recorded automatically when they explicitly resolve material ambiguity. `/pie:decision` is for manual recording, affirmation, rejection, or override.

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
## Prerequisite System Preparation
## Deferred Questions
## Recommended Delivery Path
## Trace to PIE Discovery
```

`baseline.md` is the latest readable copy. Delivery asks must reference immutable baseline revisions.

PIE does not create a separate preparation baseline. When brownfield work reveals prerequisite system preparation, record it in the intent and Delivery Baseline. Direct implementation, [Spec Kit](https://github.com/github/spec-kit), [LID](https://github.com/jszmajda/lid), or the team's normal process decides how to sequence it.

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

The readiness gate returns one of three classifications:

| Classification | Meaning | Agent behavior |
|---|---|---|
| `ready` | No unresolved blocking material ambiguity remains. | `/pie:implement` or `/pie:export` may proceed. |
| `not_ready` | Blocking material ambiguity remains. | Delivery command fails with blockers and recommended next steps. |
| `borderline` | Delivery may be possible, but a judgment call remains. | Ask the user before creating a baseline or export. |

An intent is ready only when:

- the goal is clear enough to implement;
- the intent aligns with the Project Goal or project drift has been explicitly resolved;
- relevant project guardrails are reflected;
- material decisions are made or explicitly deferred as non-blocking;
- constraints and non-negotiables are known;
- success criteria are understandable;
- no active, completed-but-undistilled, or blocking spike prevents delivery;
- delivery can proceed without silently inventing intent;
- for brownfield work, prerequisite system preparation has been assessed and represented in the intent or baseline.

If readiness is `borderline`, the agent must name the judgment call and ask whether to proceed, clarify first, or mark the issue explicitly out of scope.

## 7. Feedback Triage

`/pie:feedback` classifies delivery-stage learning into one of three outcomes:

| Classification | Meaning | Action |
|---|---|---|
| Routine delivery detail | Does not change intent, assumptions, constraints, or readiness. | Summarize briefly; do not churn artifacts. |
| Intent-impacting feedback | Clearly changes assumptions, constraints, current understanding, readiness, or baseline validity. | Update relevant PIE artifacts and explain why. |
| Ambiguous feedback | Could be intent-impacting, but judgment is needed. | Ask the user before reopening or updating PIE. |

For direct implementation, the agent may apply feedback behavior automatically when the impact is clear. For downstream delivery frameworks, use `/pie:feedback <description>` explicitly so the lineage can be recorded.

## 8. Command Semantics

| Command | Purpose |
|---|---|
| `/pie:init` | Initialize project context, derived index, agent guidance, and spike isolation. |
| `/pie:project` | Display or update Project Goal, guardrails, principles, and brownfield context. |
| `/pie:intent <name> <description>` | Create an intent, assess project alignment, and assess readiness. |
| `/pie:intent [name]` | List intents or select an intent for the current session. |
| `/pie:spike [name]` | List, create, inspect, select, or continue a spike under an explicit or session-selected intent. |
| `/pie:distill` | Fold spike findings or long discovery context into durable intent updates. |
| `/pie:decision <description>` | Manually record, affirm, reject, or override a decision. |
| `/pie:implement` | Run readiness gate, create baseline revision and ask, then implement directly. |
| `/pie:export <adapter>` | Run readiness gate, create baseline revision and ask, then write downstream seed. |
| `/pie:feedback <description>` | Reconcile delivery feedback back into PIE. |
| `/pie:baseline` | Optional preview or explicit baseline generation without delivery. |

## 9. Normal Flow

1. `/pie:init` creates project context and durable state.
2. `/pie:intent <name> <description>` creates an intent under the Project Goal.
3. The agent clarifies material ambiguity. Explicit answers update the intent immediately.
4. Inferred or project-shifting decisions are proposed for confirmation before recording as accepted.
5. `/pie:spike <name>` is used when evidence is needed.
6. `/pie:distill` folds spike findings or long discovery into the intent.
7. When the readiness gate returns `ready`, `/pie:implement` or `/pie:export <adapter>` creates a baseline revision and delivery ask.
8. When the readiness gate returns `borderline`, the agent asks for a user judgment before delivery.
9. `/pie:feedback` reopens or updates PIE only when delivery changes intent.

## 10. Traceability

Every delivery handoff must preserve this chain:

```text
PIE Intent ID
  -> Delivery Baseline ID
    -> Delivery Ask ID
      -> Downstream Target ID
```

The intent, ask records, and derived index should retain delivery history. Downstream seeds should include:

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

## 11. Adapter Rule

Adapters produce PIE-owned downstream seeds. They do not replace downstream workflows.

Current adapters:

- [`speckit`](https://github.com/github/spec-kit): writes `docs/pie/<intent>/exports/speckit-seed.md`.
- [`lid`](https://github.com/jszmajda/lid): writes `docs/pie/<intent>/exports/lid-seed.md`.

If export reveals missing delivery-critical intent, stop and reopen PIE instead of inventing downstream requirements.

## 12. Collaboration Limits

PIE's file-based model supports interrupted work and multiple intents better when authoritative artifacts win and the index is derived. It is not a full collaborative workflow engine. Concurrent writes, approvals, merge-conflict resolution, and team orchestration remain normal repository and delivery-process concerns.
