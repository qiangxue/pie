# Adapter: [Linked-Intent Development](https://github.com/jszmajda/lid)

## Purpose

Convert a PIE Delivery Baseline into a seed for [Linked-Intent Development](https://github.com/jszmajda/lid) (LID), specifically the `jszmajda/lid` workflow.

The adapter should support:

```text
/pie:export lid
```

The output is a PIE-owned seed artifact that helps [LID](https://github.com/jszmajda/lid) produce or update HLD, LLD, EARS specs, tests, and code traceability. It does not replace [LID](https://github.com/jszmajda/lid)'s review-driven design workflow.

## Expected Downstream Workflow

For Claude Code, [LID](https://github.com/jszmajda/lid) setup is normally:

```text
/plugin marketplace add jszmajda/lid
/plugin install linked-intent-dev@jszmajda-lid
/plugin install arrow-maintenance@jszmajda-lid
/linked-intent-dev:lid-setup
```

[LID](https://github.com/jszmajda/lid)'s arrow of intent is:

```text
HLD -> LLDs -> EARS specs -> failing-first tests -> code with @spec anchors
```

## Source Artifact

Read:

```text
docs/pie/<intent>/baselines/<baseline_id>.md
```

If the baseline is absent or stale, `/pie:export lid` must run the readiness gate and create or refresh the baseline before exporting.

## Output Artifact

Write:

```text
docs/pie/<intent>/exports/lid-seed.md
```

Also create a Delivery Ask record under:

```text
docs/pie/<intent>/asks/<ask_id>.md
```

Repair `docs/pie/index.md` with the ask ID, baseline ID, export path, downstream target, and intent status.

On repeated export of the same intent to [LID](https://github.com/jszmajda/lid), default to updating the known downstream target if one exists. Create a new downstream target only when the user requests it, the ask is materially independent, or [LID](https://github.com/jszmajda/lid) requires it.

## Mapping

| PIE Delivery Baseline | [LID](https://github.com/jszmajda/lid) Seed |
|---|---|
| Goal | HLD purpose / design-delta rationale |
| Context | HLD background and project framing |
| In Scope | Candidate LLD segment scope and behavior expectations |
| Out of Scope | Explicit exclusions in HLD/LLD |
| Decisions | HLD/LLD decisions |
| Constraints | HLD/LLD constraints |
| Success Criteria | Candidate EARS claims and behavioral validation targets |
| Assumptions | Design assumptions to confirm during [LID](https://github.com/jszmajda/lid) review |
| Prerequisite System Preparation | HLD/LLD sequencing constraints for existing systems |
| Deferred Questions | [LID](https://github.com/jszmajda/lid) open questions |
| Trace to PIE Discovery | Rationale and supporting evidence |

## Seed Template

````md
---
type: pie_export
adapter: lid
intent: <intent>
source_intent_id: PIE-INTENT-<INTENT-SLUG>
source_baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
source_baseline: docs/pie/<intent>/baselines/<baseline_id>.md
ask_id: PIE-ASK-<INTENT-SLUG>-LID-<NNN>
downstream_target_id: <known-or-proposed-target-id>
downstream_target_kind: new_scope_seed|existing_scope_update
created_at: YYYY-MM-DD
status: seed
---

# [LID](https://github.com/jszmajda/lid) Seed - <Intent Title>

## PIE Origin
- PIE Intent: `PIE-INTENT-<INTENT-SLUG>`
- PIE Delivery Baseline: `PIE-BASELINE-<INTENT-SLUG>-R<N>`
- PIE Delivery Ask: `PIE-ASK-<INTENT-SLUG>-LID-<NNN>`

## HLD Seed
### Purpose
What this system or change is for.

### Problem / Opportunity
Why it matters.

### Approach
The high-level direction settled by PIE.

### Non-Goals
What [LID](https://github.com/jszmajda/lid) should not include in the design.

### Constraints
Architectural, operational, product, compliance, or usability constraints.

### Prerequisite System Preparation
Existing-system preparation that [LID](https://github.com/jszmajda/lid) should account for during design sequencing.

### Accepted Decisions
Decisions already settled upstream in PIE.

### Open Questions
Questions [LID](https://github.com/jszmajda/lid) should keep visible during design review.

## Candidate LLD Segments
List likely LLDs or design areas. These are suggestions for [LID](https://github.com/jszmajda/lid) segmentation, not final boundaries.

| Segment | Responsibility | Key Behaviors | Notes |
|---|---|---|---|
| <segment-name> | ... | ... | ... |

## Candidate EARS Claims
Draft only claims strongly implied by the baseline. [LID](https://github.com/jszmajda/lid) should review and refine them.

- `<AREA>-001`: When <trigger>, the <system> shall <response>.

## Testing / Traceability Expectations
- Tests should be failing-first and cite EARS IDs.
- Code should carry `@spec` anchors for implemented behaviors.
- Keep traceability from HLD to LLDs to EARS to tests to code.

## Suggested [LID](https://github.com/jszmajda/lid) Invocation
```text
/linked-intent-dev:lid-setup
```

After setup, give this seed to the [LID](https://github.com/jszmajda/lid) workflow as the starting intent/design context.

## Trace to PIE
- Baseline: docs/pie/<intent>/baselines/<baseline_id>.md
- Intent: docs/pie/<intent>/intent.md
- Ask: docs/pie/<intent>/asks/<ask_id>.md
- Supporting spikes:
````

## Rules

- Do not generate final HLD, LLDs, EARS specs, tests, or code directly from PIE export.
- Do not invent grep-addressable EARS IDs beyond draft suggestions unless [LID](https://github.com/jszmajda/lid) is actively producing specs.
- If the PIE work changes an existing system's purpose, make that explicit as a design-delta seed.
- If [LID](https://github.com/jszmajda/lid) review discovers intent-changing feedback, reconcile through `/pie:feedback <description>` before cascading downstream changes.
- Preserve the `PIE Origin` block in downstream [LID](https://github.com/jszmajda/lid) artifacts when practical.
