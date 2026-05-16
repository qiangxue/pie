---
description: Readiness-check the active intent, auto-create a Delivery Baseline, and begin direct implementation.
---

# PIE Implement

Move the current active intent into direct implementation mode.

Use this only when the intended next step is direct coding rather than export to a downstream framework.

## Process

1. Load the active intent from `docs/pie/index.md`.
2. Run the Ready for Delivery check.
3. Determine whether a current Delivery Baseline exists.
4. If no current baseline exists and the intent is ready, create or refresh `docs/pie/<intent>/baseline.md`.
5. Mark the intent `in_delivery` in the intent artifact and index.
6. Begin direct implementation from the baseline.

## If Not Ready

Fail gracefully. Report:

- why the intent is not ready;
- unresolved blockers;
- active or undistilled spikes;
- recommended next steps.

Do not silently invent intent to begin implementation.

## If Ready

Report:

- `Intent`: active intent.
- `Actions Taken`: baseline generated/refreshed, status changed.
- `Implementation Source`: baseline path.
- `Next Step`: implementation work begun.

During direct implementation, if the agent discovers feedback that materially changes intent, it may automatically apply `/pie:feedback` behavior and update PIE artifacts.

