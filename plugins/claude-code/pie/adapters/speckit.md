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
docs/pie/<intent>/baseline.md
```

If the baseline is absent or stale, `/pie:export speckit` must run the Ready for Delivery check and create or refresh the baseline before exporting.

## Output Artifact

Write:

```text
docs/pie/<intent>/exports/speckit-seed.md
```

Update `docs/pie/index.md` with the export path and set the intent status to `in_delivery`.

## Mapping

| PIE Delivery Baseline | [Spec Kit](https://github.com/github/spec-kit) Seed |
|---|---|
| Goal | Feature summary and specification framing |
| Context | Background and user/system context |
| In-Scope Intent | Functional requirements seed |
| Out of Scope | Non-goals |
| Clarified Decisions | Pre-resolved requirement decisions |
| Constraints and Non-Negotiables | Constitution/spec constraints and planning guardrails |
| Success Criteria | Acceptance expectations and validation targets |
| Explicit Assumptions | Assumptions to preserve or revisit in clarification |
| Deferred Questions | Known clarification inputs, marked non-blocking unless stated otherwise |
| Trace to PIE Discovery | Source references for rationale |

## Seed Template

````md
---
type: pie_export
adapter: speckit
intent: <intent>
source_baseline: docs/pie/<intent>/baseline.md
created_at: YYYY-MM-DD
status: seed
---

# [Spec Kit](https://github.com/github/spec-kit) Seed - <Intent Title>

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

## Explicit Assumptions
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
- Baseline: docs/pie/<intent>/baseline.md
- Intent: docs/pie/<intent>/intent.md
- Supporting spikes:
````

## Rules

- Do not generate [Spec Kit](https://github.com/github/spec-kit) tasks from PIE directly.
- Do not bypass `/speckit.specify`; the seed should feed it.
- Keep unresolved but non-blocking questions explicit.
- If export reveals missing delivery-critical intent, stop and reopen PIE instead of inventing requirements.
- If [Spec Kit](https://github.com/github/spec-kit) later discovers intent-changing feedback, reconcile through `/pie:feedback <description>` and then regenerate or patch this seed.
