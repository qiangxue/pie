---
description: List, create, inspect, select, or continue a spike under an explicit or session-selected intent.
---

# PIE Spike

Use this to manage spikes associated with an explicit or session-selected intent.

## Forms

```text
/pie:spike
/pie:spike <name>
```

## Process

### No Name Supplied

1. Identify the explicit or session-selected intent. If unclear, ask for the intent name.
2. Load spike artifacts under `docs/pie/<intent>/spikes/`.
3. Repair `docs/pie/index.md` if stale.
4. List spikes under `docs/pie/<intent>/spikes/`.
5. Show each spike status and decision target.

### Existing Spike Name Supplied

1. Load `docs/pie/<intent>/spikes/<name>/spike.md`.
2. Select it as the current session spike only. Do not write a global active cursor to `docs/pie/index.md`.
3. Summarize:
   - parent intent;
   - question;
   - hypothesis;
   - status;
   - next steps.

### New Spike Name Supplied

If the spike does not exist:

1. Ask only the minimum necessary questions to define it:
   - What uncertainty should this spike resolve?
   - What decision will it inform?
   - What experiment or analysis should be performed?
   - What would count as a useful result?
2. Create `docs/pie/<intent>/spikes/<name>/spike.md`.
3. Create `spikes/<name>/` for spike-only code, fixtures, scripts, notes, and other experimental files.
4. Register the spike under the selected intent in the derived `docs/pie/index.md`.
5. Verify spike isolation excludes are present in repository tooling. If `spikes/` is not excluded from Git, or if `spikes/` and `docs/pie/` are not excluded from linting, tests, or package publishing where applicable, update the relevant ignore/config files or stop and ask the user to run `/pie:init`.
6. Select it as the current session spike only.

## Spike Metadata

Use lightweight frontmatter:

```yaml
---
type: spike
name: <name>
parent_intent: <intent>
status: proposed
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
decision_target: "<decision this spike informs>"
distilled_into_parent: false
---
```

Recommended statuses:

- `proposed`
- `active`
- `completed`
- `distilled`
- `abandoned`

## Output

Return:

- `Intent`.
- `Spike`, if selected or created.
- `Question`.
- `Hypothesis`.
- `Decision Target`.
- `Status`.
- `Next Step`.

Spikes are children of intents. Never leave experiment findings orphaned from the parent intent.

## Spike Work Location

Put spike-only implementation work outside `docs/` under:

```text
spikes/<name>/
```

This includes exploratory code, scripts, fixtures, sample data descriptions, analysis notes, and prototypes created only to answer the spike question.

Keep the durable spike record in `docs/pie/<intent>/spikes/<name>/spike.md`. If the spike must touch real project files to gather evidence, record the touched paths in `spike.md` with the reason and disposition. Spike code is exploratory by default and should not be treated as production code merely because it exists. Downstream delivery may reuse, rewrite, or discard it according to ordinary engineering judgment.

Before creating or running spike code, verify the isolation excludes from `/pie:init` are present.
