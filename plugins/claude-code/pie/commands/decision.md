---
description: Manually record, affirm, or override an intent-level decision.
---

# PIE Decision

Manually record, affirm, or override a decision for the current active intent.

This is not the primary mechanism for normal decision capture. Clarification answers and explicit user decisions in conversation should auto-trigger Convergence when they resolve material ambiguity. `/pie:distill` handles broader synthesis. Use `/pie:decision` when the human wants to explicitly record or override a decision.

## Form

```text
/pie:decision <description>
```

Example:

```text
/pie:decision "The screener should rank actionable pre-impulse setups rather than return a binary textbook-VCP result."
```

## Use When

- The human wants to explicitly record a decision.
- A decision was made outside the current agent flow, such as in a meeting.
- The user wants to accept, reject, or override a recommendation from `/pie:distill`.
- The user wants to make a judgment call even when evidence is inconclusive.

## Process

1. Load the active intent from `docs/pie/index.md`.
2. Normalize the decision into a structured record.
3. Capture:
   - decision;
   - rationale;
   - evidence or source;
   - impact;
   - status, usually `accepted` unless the user indicates otherwise;
   - readiness impact.
4. Update `docs/pie/<intent>/intent.md`.
5. Update status in `docs/pie/index.md` when the decision changes maturity.

## Decision Record Shape

```md
### Decision: Short title
- **Decision:** The decision.
- **Rationale:** Why this decision is appropriate.
- **Source:** Explicit user decision, external meeting, accepted recommendation, or override.
- **Impact:** What changes because of this decision.
- **Status:** Accepted, rejected, overridden, or deferred.
```

## Output

- `Decision Recorded`.
- `Rationale`.
- `Source`.
- `Impact`.
- `Decision Status`.
- `Status Change`, if any.
- `Recommended Next Step`.

Do not require `/pie:decision` after every `/pie:distill`. It is a manual override, affirmation, or external-decision capture command.
