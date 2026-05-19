---
description: Create, list, inspect, or select PIE intents using authoritative artifacts and derived index state.
---

# PIE Intent

Use this to create a new intent, list known intents, or select an intent for the current session.

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

1. Load authoritative intent artifacts under `docs/pie/<intent>/intent.md`.
2. Repair `docs/pie/index.md` if it is stale.
3. List all intents, statuses, readiness classifications, baseline states, latest asks, and child spikes.

### Existing Name Supplied

1. Load the named intent from `docs/pie/<name>/intent.md`.
2. Select it as the current session intent only. Do not write a global active cursor to `docs/pie/index.md`.
3. Clear any session-local spike selection unless the user explicitly selects one.
4. Summarize:
   - status;
   - unresolved questions;
   - child spikes;
   - readiness;
   - baseline state.

### New Name and Description Supplied

1. Load `docs/pie/project.md`.
2. Normalize `<name>` as a short lowercase hyphenated intent folder name.
3. Assess alignment with the Project Goal and guardrails:
   - Does this intent support the Project Goal?
   - Does it stretch or distort the Project Goal?
   - Does it suggest the Project Goal itself may need to evolve?
4. If alignment is unclear or negative, ask whether to reframe the intent, update the Project Goal, or treat this work as a separate project.
5. Create `docs/pie/<name>/intent.md`.
6. Register the intent in the derived `docs/pie/index.md`.
7. Select it as the current session intent only.
8. Assign a stable `intent_id` using `PIE-INTENT-<UPPER-HYPHENATED-NAME>`.
9. Assess readiness as `ready`, `not_ready`, or `borderline`.
10. Ask focused clarification questions when needed.
11. Apply the materiality test: a question is material only when different answers would produce a meaningfully different Delivery Baseline.
12. Apply the Convergence Rule:
   - explicit answer, accepted option, explicit decision, or accepted pending recommendation: record the decision, remove the ambiguity, update current understanding, refresh readiness, and repair `docs/pie/index.md`;
   - inferred decision, project-framing shift, or partial answer: propose the decision or update understanding, but ask before recording it as accepted;
   - exploratory remarks and non-material details: do not update PIE artifacts.
13. Suggest spikes when empirical evidence is required.
14. Set or update intent status accordingly.

The `intent_id` is the durable identity of the work. Do not change it if the intent matures, delivery happens more than once, or feedback reopens discovery.

Do not let a new intent silently change what the project is for. If repeated intents suggest project drift, recommend `/pie:project` to update or split the Project Goal.

## Intent Metadata

Use lightweight frontmatter:

```yaml
---
type: intent
intent_id: PIE-INTENT-<UPPER-HYPHENATED-NAME>
name: <name>
project_goal_alignment: aligned|unclear|misaligned
status: discovering
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
readiness: not_ready
delivery_baseline: absent
recommended_next_step: clarify
---
```

Active context is session-local, not durable project state.

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

- `Intent`: selected or created intent name.
- `Project Alignment`: aligned, unclear, or misaligned, with a short reason.
- `Status`: durable status from the intent artifact and index.
- `Current Understanding`: brief summary.
- `Material Unknowns`: blocking questions, if any.
- `Child Spikes`: current spike list and statuses.
- `Delivery Baseline`: absent, current, stale, or present.
- `Delivery Asks`: latest ask IDs and downstream targets, if any.
- `Recommended Next Step`: clarify, create spike, distill, implement, export, or feedback.

Every change must update authoritative artifacts and repair `docs/pie/index.md`. Do not rely on chat history as the source of truth.
