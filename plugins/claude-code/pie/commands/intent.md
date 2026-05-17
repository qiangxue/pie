---
description: Create, list, inspect, or switch PIE intents using durable index state.
---

# PIE Intent

Use this to create a new intent, list known intents, or switch active intent.

## Forms

```text
/pie:intent
/pie:intent <name>
/pie:intent <name> <description>
```

Example:

```text
/pie:intent vcp-screener "Create a stock screener based on VCP."
```

## Process

### No Name Supplied

1. Load `docs/pie/index.md`.
2. List all intents and their statuses.
3. Show the active intent and active spike.

### Existing Name Supplied

1. Load the named intent from `docs/pie/<name>/intent.md`.
2. Set it as active intent in `docs/pie/index.md`.
3. Clear active spike unless the user explicitly selects one.
4. Summarize:
   - status;
   - unresolved questions;
   - child spikes;
   - readiness;
   - baseline state.

### New Name and Description Supplied

1. Normalize `<name>` as a short lowercase hyphenated intent folder name.
2. Create `docs/pie/<name>/intent.md`.
3. Register the intent in `docs/pie/index.md`.
4. Set it as active intent.
5. Assign a stable `intent_id` using `PIE-INTENT-<UPPER-HYPHENATED-NAME>`.
6. Assess whether the intent is ready for delivery, needs clarification, or needs one or more spikes.
7. Ask focused clarification questions when needed.
8. When the user answers a material clarifying question, automatically apply the Convergence Rule:
   - determine whether the answer resolves a tracked ambiguity;
   - record any resulting settled decision;
   - update current understanding;
   - update material unknowns;
   - update readiness status;
   - update `docs/pie/index.md`.
9. Suggest spikes when empirical evidence is required.
10. Set or update intent status accordingly.

The `intent_id` is the durable identity of the work. Do not change it if the intent matures, delivery happens more than once, or feedback reopens discovery.

## Intent Metadata

Use lightweight frontmatter:

```yaml
---
type: intent
intent_id: PIE-INTENT-<UPPER-HYPHENATED-NAME>
name: <name>
status: discovering
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
active: true
delivery_baseline: absent
recommended_next_step: clarify
---
```

Recommended statuses:

- `discovering`
- `needs_clarification`
- `needs_spike`
- `ready_for_delivery`
- `in_delivery`
- `reopened`
- `completed`

## Output

Return:

- `Intent`: active intent name.
- `Status`: durable status from the intent artifact and index.
- `Current Understanding`: brief summary.
- `Material Unknowns`: blocking questions, if any.
- `Child Spikes`: current spike list and statuses.
- `Delivery Baseline`: absent, current, stale, or present.
- `Delivery Asks`: latest ask IDs and downstream targets, if any.
- `Recommended Next Step`: clarify, create spike, distill, implement, export, or feedback.

Every change must update durable artifacts and `docs/pie/index.md`. Do not rely on chat history as the source of truth.
