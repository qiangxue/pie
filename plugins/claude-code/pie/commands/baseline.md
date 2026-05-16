---
description: Preview or explicitly generate a Delivery Baseline without implementing or exporting.
---

# PIE Baseline

Preview or explicitly generate a Delivery Baseline. This command is optional and should not be required in the normal workflow. `/pie:implement` and `/pie:export <adapter>` normally create or refresh the Delivery Baseline automatically.

## Readiness Check

Before creating a Delivery Baseline, confirm:

- The goal is clear enough to implement.
- Material decisions have been made or explicitly deferred as non-blocking.
- Key constraints and non-negotiables are known.
- Success criteria are understandable.
- Downstream delivery can proceed without silently inventing intent.
- For existing systems, prerequisite system preparation has been assessed and separated where needed.

If any item fails, do not create a baseline. Report blockers and recommend clarification, spike work, or distillation.

## Delivery Baseline Template

Use `docs/pie/<intent>/baseline.md`:

```md
# Delivery Baseline - [Delivery Unit]
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

Create or update the appropriate baseline artifact only if ready. Update `docs/pie/index.md`. Keep the baseline concise, stable, and downstream-ready.
