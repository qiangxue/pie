# PIE Claude Code Command Reference

This document defines the Claude Code slash-command behavior for PIE.

Commands operate on durable artifacts under `docs/pie/`, not on chat history.

## Durable Layout

```text
docs/pie/
  project.md
  index.md
  <intent>/
    intent.md
    baseline.md
    baselines/
      <baseline_id>.md
    asks/
      <ask_id>.md
    exports/
    preparation-baseline.md
    spikes/
      <spike>/
        spike.md
spikes/
  <spike>/
```

## Command Set

| Command | Purpose |
|---|---|
| `/pie:init` | Initialize project context, index, guidance files, and spike isolation. |
| `/pie:project` | Show or update the Project Goal, guardrails, principles, and brownfield context. |
| `/pie:intent <name> <description>` | Create an intent, assess project alignment, and assess readiness. |
| `/pie:intent` | List intents and active context. |
| `/pie:intent <name>` | Switch active intent. |
| `/pie:spike [name]` | List, create, inspect, select, or continue a spike. |
| `/pie:distill` | Fold spike findings or long discovery context into durable intent updates. |
| `/pie:decision <description>` | Manually record, affirm, reject, or override a decision. |
| `/pie:implement` | Run the readiness gate, create baseline revision and ask, then implement directly. |
| `/pie:export <adapter>` | Run the readiness gate, create baseline revision and ask, then write downstream seed. |
| `/pie:feedback <description>` | Reconcile delivery feedback back into PIE. |
| `/pie:baseline` | Optional baseline preview or explicit generation without delivery. |

## `/pie:init`

Create or update:

```text
AGENTS.md
CLAUDE.md
docs/pie/project.md
docs/pie/index.md
```

Behavior:

1. Preserve existing repository guidance.
2. For greenfield projects, ask for the Project Goal and material guardrails.
3. For brownfield projects, inspect durable context, propose a Project Goal and guardrails, and ask for confirmation or revision.
4. Create the PIE index if missing.
5. Enforce spike isolation:
   - `.gitignore`: `spikes/`;
   - `.eslintignore`: `spikes/` and `docs/pie/`, when applicable;
   - `.npmignore`: `spikes/` and `docs/pie/`, when applicable;
   - equivalent excludes for detected lint, test, build, or package-publish systems.

Do not add `docs/pie/` to `.gitignore` by default. `docs/pie/` is durable PIE state and should normally be committed.

Do not invent a brownfield Project Goal without confirmation.

## `/pie:project`

Display `docs/pie/project.md`.

Show:

- Project Goal;
- guardrails;
- shared principles;
- brownfield system understanding, when present;
- known evolution themes and project-level questions, when present;
- active intents.

End with a light invitation to update project context. If the user updates it, revise `project.md`, update `index.md` when needed, and flag intents that may need alignment review.

## `/pie:intent`

Forms:

```text
/pie:intent
/pie:intent <name>
/pie:intent <name> <description>
```

Create behavior:

1. Load `docs/pie/project.md`.
2. Assess whether the intent aligns with the Project Goal and guardrails.
3. If alignment is unclear or negative, ask whether to reframe the intent, update the Project Goal, or treat the work as a separate project.
4. Create `docs/pie/<intent>/intent.md`.
5. Assign stable `intent_id`, for example `PIE-INTENT-STOCK-SCREENER`.
6. Register the intent in `docs/pie/index.md` and set it active.
7. Assess readiness.
8. Ask only material clarification questions.
9. Recommend a spike when evidence is needed.

List or switch behavior:

- `/pie:intent` lists intents, statuses, active intent, active spike, baseline state, and latest asks.
- `/pie:intent <name>` switches active intent and summarizes durable state.

Clarification answers should auto-trigger convergence: if the answer resolves an ambiguity, creates a decision, changes understanding, or affects readiness, update the intent and index immediately.

## `/pie:spike`

Forms:

```text
/pie:spike
/pie:spike <name>
```

Behavior:

- no name: list spikes under the active intent;
- existing name: select the spike and summarize question, status, and next step;
- new name: create a focused spike under the active intent.

Spike records live at:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
```

Spike-only code lives at:

```text
spikes/<spike>/
```

Before creating or running spike code, verify spike isolation from `/pie:init`.

## `/pie:distill`

Use for synthesis, not routine one-question clarification.

Use it when:

- a spike has findings;
- a long or branched conversation needs consolidation;
- several partial conclusions accumulated;
- the user wants a durable checkpoint.

Behavior:

1. If a spike is active, summarize findings and update the spike record.
2. Update the parent intent with resolved unknowns, evidence, and current understanding.
3. Record decisions that are already settled.
4. Recommend decisions that still need approval.
5. Update readiness and `docs/pie/index.md`.

## `/pie:decision <description>`

Manually record, affirm, reject, or override an intent-level decision.

Use it when:

- the human explicitly wants to record a decision;
- a decision was made outside the current agent flow;
- the user wants to accept, reject, or override a recommendation;
- evidence is inconclusive but the user chooses a direction.

Normal clarification and distillation should record settled decisions automatically.

## `/pie:baseline`

Optional. Preview or explicitly generate a Delivery Baseline without starting delivery.

Readiness must pass before baseline creation. If ready:

- update `docs/pie/<intent>/baseline.md`;
- create or refresh the next immutable snapshot under `docs/pie/<intent>/baselines/`;
- update `docs/pie/index.md`.

This command does not create a Delivery Ask.

## `/pie:implement`

Start direct implementation.

Behavior:

1. Load project context and active intent.
2. Run the readiness gate.
3. Create or refresh `baseline.md`.
4. Create immutable baseline revision under `baselines/`.
5. Create direct implementation ask under `asks/`.
6. Mark intent `in_delivery`.
7. Implement from the baseline revision.

If readiness fails, report blockers and recommended next steps. Do not invent missing intent.

## `/pie:export <adapter>`

Export to a downstream delivery framework.

Current adapters:

```text
/pie:export speckit
/pie:export lid
```

Behavior:

1. Load project context and active intent.
2. Run the readiness gate.
3. Create or refresh `baseline.md`.
4. Create immutable baseline revision under `baselines/`.
5. Create export ask under `asks/`.
6. Load the adapter spec.
7. Write the seed under `docs/pie/<intent>/exports/`.
8. Include a `PIE Origin` block.
9. Mark intent `in_delivery`.

Repeated export to the same adapter should default to updating the known downstream target while still creating a new ask record.

## `/pie:feedback <description>`

Use when direct implementation or downstream delivery reveals learning that may change intent.

Behavior:

1. Identify the active intent or infer it from an ask ID.
2. Record feedback source lineage when known: ask ID, baseline ID, framework, and downstream target.
3. Classify feedback:
   - routine implementation detail;
   - challenged assumption;
   - new material ambiguity;
   - new empirical uncertainty;
   - preparation gap;
   - project-level drift.
4. If feedback does not change intent, summarize without churning artifacts.
5. If it changes intent, update the intent, readiness, index, and downstream impact.
6. Recommend clarification, spike, baseline revision, seed regeneration, or project update as needed.
