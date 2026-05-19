---
description: Synthesize spike findings, long discovery conversations, or explicit checkpoints into durable intent updates.
---

# PIE Distill

Apply Continuous Context Distillation.

This command is context-sensitive.

If a spike is selected for the current session, distill spike learning back into the parent intent.

If no spike is selected but an intent is selected, distill accumulated discovery context by compacting substantial learning into durable state.

This is not required for ordinary one-question clarification flows. When the user explicitly answers a material clarifying question, the agent should automatically apply the Convergence Rule and update the selected intent if the answer resolves an ambiguity, creates a decision, changes current understanding, or affects readiness.

Use `/pie:distill` for broader synthesis: spike findings, long or branched conversations, multiple partial conclusions, or explicit durable checkpoints.

## Trigger

Use this when a spike has produced findings, several decisions have accumulated, multiple options were discussed, the conversation branched, the user wants a checkpoint, or the selected artifact no longer reflects the working understanding.

## Process

1. Determine session context:
   - selected spike, if present;
   - otherwise selected intent.
2. If context is unclear, ask which intent or spike to distill.
3. Load authoritative artifacts and repair `docs/pie/index.md` if stale.
4. If a spike is selected:
   - summarize what the spike learned;
   - update `docs/pie/<intent>/spikes/<spike>/spike.md`;
   - determine which parent-intent ambiguities were resolved;
   - identify decisions that are already settled by user answers, agreed conclusions, or accepted spike findings;
   - record settled decisions in the parent intent;
   - recommend but do not mark as accepted any decisions that still require human approval, especially judgment calls or material project-direction changes;
   - update the parent intent with current understanding, evidence and findings, revised unknowns, settled decisions, recommended decisions, and readiness impact;
   - mark the spike `completed`, `distilled`, or keep it `active` if more investigation is needed.
5. If only an intent is selected:
   - refresh current understanding;
   - identify decisions already made during accumulated conversation;
   - record settled decisions in the selected intent;
   - recommend unresolved decisions when evidence suggests a path but the user has not approved it;
   - capture meaningful evidence;
   - identify unresolved blockers;
   - update readiness status.
   - reclassify readiness as `ready`, `not_ready`, or `borderline`.
6. Repair `docs/pie/index.md`.
7. Do not copy chat history. Preserve learning, decisions, and blockers at useful fidelity.

## Output

Return:

- `Distilled Context`: selected spike or selected intent.
- `Key Findings`.
- `Intent Updates`.
- `Recorded Decisions`: decisions already settled and written to the intent.
- `Recommended Decisions`: decisions suggested but not yet accepted.
- `Status Changes`.
- `Remaining Blockers`.
- `Recommended Next Step`.

Do not require `/pie:distill` after a single clarification answer that clearly resolves an ambiguity. That should be handled by automatic Convergence.
