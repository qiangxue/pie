# Adapter: [Spec Kit](https://github.com/github/spec-kit)

## Purpose

Convert a PIE Delivery Baseline into a high-quality seed for [GitHub Spec Kit](https://github.com/github/spec-kit)'s spec-driven workflow.

The adapter should support:

```text
/pie:export speckit
```

The output is a PIE-owned seed artifact, not a full [Spec Kit](https://github.com/github/spec-kit) plan or task breakdown.

## Expected Downstream Workflow

[Spec Kit](https://github.com/github/spec-kit)'s core workflow is:

```text
/speckit.specify
/speckit.plan
/speckit.tasks
/speckit.implement
```

Optional downstream commands such as `/speckit.clarify`, `/speckit.analyze`, and `/speckit.checklist` may be used after export when the downstream spec needs validation.

## Source Artifact

Read:

```text
docs/pie/<intent>/baselines/<baseline_id>.md
```

If the baseline is absent or stale, `/pie:export speckit` must run the readiness gate and create or refresh the baseline before exporting.

## Output Artifact

Write:

```text
docs/pie/<intent>/exports/speckit-seed.md
```

Also create a Delivery Ask record under:

```text
docs/pie/<intent>/asks/<ask_id>.md
```

Repair `docs/pie/index.md` with the ask ID, baseline ID, export path, downstream target, and intent status.

On repeated export of the same intent to [Spec Kit](https://github.com/github/spec-kit), default to updating the known downstream target if one exists. Create a new downstream target only when the user requests it, the ask is materially independent, or [Spec Kit](https://github.com/github/spec-kit) requires it.

## Mapping

| PIE Delivery Baseline | [Spec Kit](https://github.com/github/spec-kit) Seed |
|---|---|
| Goal | Feature summary and specification framing |
| Context | Background and user/system context |
| In Scope | Functional requirements seed |
| Out of Scope | Non-goals |
| Decisions | Pre-resolved requirement decisions |
| Constraints | Constitution/spec constraints and planning guardrails |
| Success Criteria | Acceptance expectations and validation targets |
| Assumptions | Assumptions to preserve or revisit in clarification |
| Prerequisite System Preparation | Planning constraints or sequencing notes for existing systems |
| Deferred Questions | Known clarification inputs, marked non-blocking unless stated otherwise |
| Trace to PIE Discovery | Source references for rationale |

## Seed Template

````md
---
type: pie_export
adapter: speckit
intent: <intent>
source_intent_id: PIE-INTENT-<INTENT-SLUG>
source_baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
source_baseline: docs/pie/<intent>/baselines/<baseline_id>.md
ask_id: PIE-ASK-<INTENT-SLUG>-SPECKIT-<NNN>
downstream_target_id: <known-or-proposed-target-id>
downstream_target_kind: new_feature_seed|existing_feature_update
created_at: YYYY-MM-DD
status: seed
---

# [Spec Kit](https://github.com/github/spec-kit) Seed - <Intent Title>

## PIE Origin
- PIE Intent: `PIE-INTENT-<INTENT-SLUG>`
- PIE Delivery Baseline: `PIE-BASELINE-<INTENT-SLUG>-R<N>`
- PIE Delivery Ask: `PIE-ASK-<INTENT-SLUG>-SPECKIT-<NNN>`

## Feature Summary
One concise paragraph describing what [Spec Kit](https://github.com/github/spec-kit) should specify.

## User / System Context
Relevant product, user, domain, system, and operational context.

## Requirements Seed
### Functional Requirements
- MUST ...

### Non-Functional Requirements
- MUST ...

## Non-Goals
- ...

## Pre-Resolved Decisions
- ...

## Constraints and Guardrails
- ...

## Acceptance Expectations
- ...

## Assumptions
- ...

## Prerequisite System Preparation
- ...

## Deferred Clarifications
- ...

## Suggested [Spec Kit](https://github.com/github/spec-kit) Invocation
```text
/speckit.specify <paste or reference this seed>
```

## Suggested Planning Inputs
Use this section later with `/speckit.plan` if it is already known. Do not invent technical choices that PIE has not settled.
- Architecture:
- Data model:
- Storage:
- Dependencies:
- Testing expectations:

## Trace to PIE
- Baseline: docs/pie/<intent>/baselines/<baseline_id>.md
- Intent: docs/pie/<intent>/intent.md
- Ask: docs/pie/<intent>/asks/<ask_id>.md
- Supporting spikes:
````

## Rules

- Do not generate [Spec Kit](https://github.com/github/spec-kit) tasks from PIE directly.
- Do not bypass `/speckit.specify`; the seed should feed it.
- Keep unresolved but non-blocking questions explicit.
- If export reveals missing delivery-critical intent, stop and reopen PIE instead of inventing requirements.
- If [Spec Kit](https://github.com/github/spec-kit) later discovers intent-changing feedback, reconcile through `/pie:feedback <description>` and then regenerate or patch this seed.
- Preserve the `PIE Origin` block in downstream [Spec Kit](https://github.com/github/spec-kit) artifacts when practical.
