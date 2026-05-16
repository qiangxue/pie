---
description: Synthesize spike findings, long discovery conversations, or explicit checkpoints into durable intent updates.
---

# PIE Distill

Apply Continuous Context Distillation.

This command is context-sensitive.

If a spike is active, distill spike learning back into the parent intent.

If no spike is active but an intent is active, distill accumulated discovery context by compacting substantial learning into durable state.

This is not required for ordinary one-question clarification flows. When the user answers a material clarifying question, the agent should automatically apply the Convergence Rule and update the active intent if the answer resolves an ambiguity, creates a decision, changes current understanding, or affects readiness.

Use `/pie:distill` for broader synthesis: spike findings, long or branched conversations, multiple partial conclusions, or explicit durable checkpoints.

## Trigger

Use this when a spike has produced findings, several decisions have accumulated, multiple options were discussed, the conversation branched, the user wants a checkpoint, or the active artifact no longer reflects the working understanding.

## Process

1. Load `docs/pie/index.md`.
2. Determine active context:
   - active spike, if present;
   - otherwise active intent.
3. If a spike is active:
   - summarize what the spike learned;
   - update `docs/pie/<intent>/spikes/<spike>/spike.md`;
   - determine which parent-intent ambiguities were resolved;
   - identify decisions that are already settled by user answers, agreed conclusions, or accepted spike findings;
   - record settled decisions in the parent intent;
   - recommend but do not mark as accepted any decisions that still require human approval, especially judgment calls or material project-direction changes;
   - update the parent intent with current understanding, evidence and findings, revised unknowns, settled decisions, recommended decisions, and readiness impact;
   - mark the spike `completed`, `distilled`, or `continue_investigating`.
4. If only an intent is active:
   - refresh current understanding;
   - identify decisions already made during accumulated conversation;
   - record settled decisions in the active intent;
   - recommend unresolved decisions when evidence suggests a path but the user has not approved it;
   - capture meaningful evidence;
   - identify unresolved blockers;
   - update readiness status.
5. Update `docs/pie/index.md`.
6. Do not copy chat history. Preserve learning, decisions, and blockers at useful fidelity.

## Output

Return:

- `Distilled Context`: active spike or active intent.
- `Key Findings`.
- `Intent Updates`.
- `Recorded Decisions`: decisions already settled and written to the intent.
- `Recommended Decisions`: decisions suggested but not yet accepted.
- `Status Changes`.
- `Remaining Blockers`.
- `Recommended Next Step`.

Do not require `/pie:distill` after a single clarification answer that clearly resolves an ambiguity. That should be handled by automatic Convergence.
