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
- Downstream delivery can proceed without silently inventing intent.
- For existing systems, prerequisite system preparation has been assessed and separated where needed.

If any item fails, do not create a baseline. Report blockers and recommend clarification, spike work, or distillation.

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

## Preparation Baseline Template

Use `docs/pie/<intent>/preparation-baseline.md` only when existing-system preparation is required:

```md
# Preparation Baseline - [Preparation Unit]
## Purpose
## Current System Constraint
## Preparation Scope
## Out of Scope
## Success Criteria
## Sequencing
## Trace to Intent Delta Brief
```

## Output

Create or update the appropriate baseline artifact only if ready. Update `docs/pie/index.md` with the current baseline ID, revision, and path. Keep the baseline concise, stable, and downstream-ready.

This command does not create a Delivery Ask. `/pie:implement` and `/pie:export <adapter>` create ask records when they begin a handoff.
