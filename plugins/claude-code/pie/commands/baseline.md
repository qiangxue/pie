---
description: Preview or explicitly generate a Delivery Baseline without implementing or exporting.
---

# PIE Baseline

Preview or explicitly generate a Delivery Baseline. This command is optional and should not be required in the normal workflow. `/pie:implement` and `/pie:export <adapter>` normally create or refresh the Delivery Baseline automatically.

## Readiness Check

Before creating a Delivery Baseline, confirm:

- The goal is clear enough to implement.
- The intent aligns with `docs/pie/project.md` or any project-level drift has been explicitly resolved.
- Project guardrails and shared principles are reflected where materially relevant.
- Material decisions have been made or explicitly deferred as non-blocking.
- Key constraints and non-negotiables are known.
- Success criteria are understandable.
- No active, completed-but-undistilled, or blocking spike prevents delivery.
- Downstream delivery can proceed without silently inventing intent.
- For existing systems, prerequisite system preparation has been assessed and represented in the intent or baseline where needed.

Classify readiness as:

- `ready`: create or refresh the baseline.
- `not_ready`: do not create a baseline; report blockers and recommend clarification, spike work, or distillation.
- `borderline`: name the remaining judgment call and ask the user before baselining.

## Delivery Baseline Revision

Use `docs/pie/<intent>/baseline.md` as the latest readable baseline. When a baseline is generated for a delivery handoff, also create an immutable revision snapshot:

```text
docs/pie/<intent>/baselines/<baseline_id>.md
```

Use this metadata:

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

Increment `revision` from the previous baseline revision for the intent. `baseline.md` may be overwritten as the current convenience copy; files under `baselines/` are revision snapshots and should not be silently rewritten after they are used by a delivery ask.

## Delivery Baseline Template

Use this content in both the current baseline and revision snapshot:

```md
# Delivery Baseline - [Intent Title]
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

PIE does not create a separate preparation baseline. When brownfield work reveals prerequisite system preparation, record it under `Prerequisite System Preparation` and let the downstream delivery path decide sequencing.

## Output

Create or update the baseline artifact only if readiness is `ready` or confirmed `borderline`. Repair `docs/pie/index.md` with the current baseline ID, revision, and path. Keep the baseline concise, stable, and downstream-ready.

This command does not create a Delivery Ask. `/pie:implement` and `/pie:export <adapter>` create ask records when they begin a handoff.
