---
description: Run the readiness gate, auto-create a Delivery Baseline, and export to a downstream adapter.
---

# PIE Export

Export a delivery-ready intent into a downstream delivery framework.

## Form

```text
/pie:export <adapter>
```

Examples:

```text
/pie:export speckit
/pie:export lid
```

## Process

1. Load `docs/pie/project.md` and the active intent from `docs/pie/index.md`.
2. Run the readiness gate, including project-goal alignment and project guardrails.
3. Determine whether a current Delivery Baseline exists.
4. If no current baseline exists or the intent changed since the last handoff, create or refresh `docs/pie/<intent>/baseline.md`.
5. Create an immutable baseline revision snapshot under `docs/pie/<intent>/baselines/`.
6. Create a Delivery Ask record under `docs/pie/<intent>/asks/`.
7. Load the requested adapter instructions:
   - `speckit` -> `${CLAUDE_PLUGIN_ROOT}/adapters/speckit.md`
   - `lid` -> `${CLAUDE_PLUGIN_ROOT}/adapters/lid.md`
8. Export from the baseline revision into the requested adapter format.
9. Write the export under `docs/pie/<intent>/exports/`.
10. Include PIE origin metadata in the export.
11. Mark the intent `in_delivery` in the intent artifact and index.

## Traceability

Use a stable traceability chain:

```text
PIE Intent ID -> Delivery Baseline ID -> Delivery Ask ID -> Downstream Target ID
```

The ask should use metadata like:

```yaml
---
type: delivery_ask
ask_id: PIE-ASK-<INTENT-SLUG>-<ADAPTER>-<NNN>
source_intent_id: PIE-INTENT-<INTENT-SLUG>
source_baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
delivery_mode: export
target_framework: speckit|lid
created_at: YYYY-MM-DDTHH:mm:ss<offset>
downstream_target:
  framework: speckit|lid
  target_id: <known-or-proposed-target-id>
  target_kind: new_feature_seed|existing_feature_update|new_scope_seed|existing_scope_update|unknown
  status: pending|proposed|known|updated
---
```

If the downstream target ID is not known at export time, record a proposed or pending target and update the ask when the target becomes known.

When exporting the same intent to the same adapter again, default to the known downstream target for that adapter and classify the new ask as an update. Create a new downstream target only when the user requests it, the work is materially independent, or the downstream framework requires it.

## Available Adapters

- [`speckit`](https://github.com/github/spec-kit): create `docs/pie/<intent>/exports/speckit-seed.md`.
- [`lid`](https://github.com/jszmajda/lid): create `docs/pie/<intent>/exports/lid-seed.md`.

For detailed mapping and output templates, follow the adapter file exactly.

If the adapter name is unknown, list available adapters and stop.

## If Not Ready

Fail gracefully with blockers and recommended next steps. Do not export by silently inventing intent.

## Output

Return:

- `Intent`.
- `Adapter`.
- `Actions Taken`: baseline generated/refreshed, baseline revision created, delivery ask created, export artifact created, status changed.
- `Delivery Ask`.
- `Export Artifact`.
- `Downstream Target`.
- `Recommended Next Step`.

When downstream delivery later discovers intent-changing feedback, use `/pie:feedback <description>` explicitly to reconcile it into PIE before regenerating or patching downstream artifacts.
