---
description: Readiness-check the active intent, auto-create a Delivery Baseline, and export to a downstream adapter.
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

1. Load the active intent from `docs/pie/index.md`.
2. Run the Ready for Delivery check.
3. Determine whether a current Delivery Baseline exists.
4. If no current baseline exists and the intent is ready, create or refresh `docs/pie/<intent>/baseline.md`.
5. Load the requested adapter instructions:
   - `speckit` -> `${CLAUDE_PLUGIN_ROOT}/adapters/speckit.md`
   - `lid` -> `${CLAUDE_PLUGIN_ROOT}/adapters/lid.md`
6. Export from the Delivery Baseline into the requested adapter format.
7. Write the export under `docs/pie/<intent>/exports/`.
8. Mark the intent `in_delivery` in the intent artifact and index.

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
- `Actions Taken`: baseline generated/refreshed, export artifact created, status changed.
- `Export Artifact`.
- `Recommended Next Step`.

When downstream delivery later discovers intent-changing feedback, use `/pie:feedback <description>` explicitly to reconcile it into PIE before regenerating or patching downstream artifacts.
