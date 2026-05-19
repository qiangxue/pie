---
description: Run the readiness gate, auto-create a Delivery Baseline, and begin direct implementation.
---

# PIE Implement

Move the explicit or session-selected intent into direct implementation mode.

Use this only when the intended next step is direct coding rather than export to a downstream framework.

## Process

1. Load `docs/pie/project.md` and the explicit or session-selected intent. If unclear, ask for the intent name.
2. Run the readiness gate, including project-goal alignment and project guardrails.
3. If readiness is `not_ready`, report blockers and stop.
4. If readiness is `borderline`, name the judgment call and ask the user before proceeding.
5. Determine whether a current Delivery Baseline exists.
6. If no current baseline exists or the intent changed since the last handoff, create or refresh `docs/pie/<intent>/baseline.md`.
7. Create an immutable baseline revision snapshot under `docs/pie/<intent>/baselines/`.
8. Create a Delivery Ask record under `docs/pie/<intent>/asks/`.
9. Mark the intent `in_delivery` in the intent artifact and repair the derived index.
10. Begin direct implementation from the baseline revision.

## Traceability

Use a stable traceability chain:

```text
PIE Intent ID -> Delivery Baseline ID -> Delivery Ask ID -> Direct Implementation Target
```

The direct implementation ask should use metadata like:

```yaml
---
type: delivery_ask
ask_id: PIE-ASK-<INTENT-SLUG>-DIRECT-<NNN>
source_intent_id: PIE-INTENT-<INTENT-SLUG>
source_baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
delivery_mode: direct
target_framework: direct
created_at: YYYY-MM-DDTHH:mm:ss<offset>
downstream_target:
  framework: direct
  target_id: implementation-session-YYYY-MM-DD
  target_kind: implementation_session
  status: known
---
```

Repair `docs/pie/index.md` and the intent delivery history with the ask ID, baseline ID, target framework, downstream target ID, and artifact path.

## If Not Ready

Fail gracefully. Report:

- readiness classification: `not_ready` or `borderline`;
- why the intent cannot proceed without a user decision;
- unresolved blockers;
- active or undistilled spikes;
- recommended next steps.

Do not silently invent intent to begin implementation.

## If Ready

Report:

- `Intent`: selected intent.
- `Actions Taken`: baseline generated/refreshed, baseline revision created, delivery ask created, status changed.
- `Implementation Source`: baseline revision path.
- `Delivery Ask`: ask ID and ask record path.
- `Next Step`: implementation work begun.

During direct implementation, if the agent discovers feedback that materially changes intent, it may automatically apply `/pie:feedback` behavior and update PIE artifacts.
