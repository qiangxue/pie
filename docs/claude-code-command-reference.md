# PIE Claude Code Command Reference

This document defines the Claude Code slash-command behavior for PIE.

Commands operate on durable artifacts under `docs/pie/`, not on chat history. Artifact files are authoritative; `docs/pie/index.md` is a derived registry.

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
    spikes/
      <spike>/
        spike.md
spikes/
  <spike>/
```

## State Rules

- `project.md`, `intent.md`, `spike.md`, baseline files, ask records, and export seeds are authoritative.
- `index.md` is derived and repairable. If it conflicts with authoritative artifacts, repair the index from artifacts.
- Active intent and active spike are session-local. Do not store a shared active cursor in `docs/pie/index.md`.
- If session context is unclear, ask for the intent or spike name instead of guessing.

## Command Set

| Command | Purpose |
|---|---|
| `/pie:init` | Initialize project context, derived index, guidance files, and spike isolation. |
| `/pie:project` | Show or update the Project Goal, guardrails, principles, and brownfield context. |
| `/pie:intent <name> <description>` | Create an intent, assess project alignment, and assess readiness. |
| `/pie:intent` | List intents and status from the derived registry. |
| `/pie:intent <name>` | Select an intent for the current session and summarize durable state. |
| `/pie:spike [name]` | List, create, inspect, select, or continue a spike under an explicit or session-selected intent. |
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
4. Create or repair the derived PIE index.
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
- known evolution themes and project-level questions, when present.

End with a light invitation to update project context. If the user updates it, revise `project.md`, repair `index.md` when needed, and flag intents that may need alignment review.

Do not store active intents, intent status, or spike status in `project.md`.

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
6. Register the intent in the derived `docs/pie/index.md`.
7. Select the intent for the current session only.
8. Assess readiness as `ready`, `not_ready`, or `borderline`.
9. Ask only material clarification questions.
10. Recommend a spike when evidence is needed.

List or select behavior:

- `/pie:intent` lists intents, statuses, readiness, baseline state, latest asks, and child spikes.
- `/pie:intent <name>` selects the intent for the current session and summarizes durable state.

Material ambiguity test:

```text
Would a different answer change the goal, in-scope behavior, success criteria,
non-negotiable constraints, delivery readiness, or downstream ask?
```

If yes, ask or track it. If no, keep it out of PIE.

Convergence behavior:

- explicit answer or accepted option: auto-record the decision, remove the ambiguity, and refresh readiness;
- explicit user decision: auto-record the decision and refresh readiness;
- accepted pending recommendation: auto-record the decision and refresh readiness;
- inferred decision or project-framing shift: propose the decision and ask for confirmation before recording as accepted;
- partial answer: update understanding but keep unresolved ambiguity visible;
- exploratory remark or non-material detail: do not update PIE artifacts.

## `/pie:spike`

Forms:

```text
/pie:spike
/pie:spike <name>
```

Behavior:

- no name: list spikes under the session-selected intent, or ask for the intent if no session selection is clear;
- existing name: select the spike for the current session and summarize question, status, and next step;
- new name: create a focused spike under the explicit or session-selected intent.

Spike records live at:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
```

Spike-only code lives at:

```text
spikes/<spike>/
```

Statuses:

```text
proposed
active
completed
distilled
abandoned
```

Spike code is exploratory by default. Production reuse is a delivery decision, not a PIE spike status.

Before creating or running spike code, verify spike isolation from `/pie:init`.

## `/pie:distill`

Use for synthesis, not routine one-question clarification.

Use it when:

- a spike has findings;
- a long or branched conversation needs consolidation;
- several partial conclusions accumulated;
- the user wants a durable checkpoint.

Behavior:

1. Use the session-selected spike or intent. If unclear, ask which intent or spike to distill.
2. If a spike is selected, summarize findings and update the spike record.
3. Update the parent intent with resolved unknowns, evidence, and current understanding.
4. Record decisions that are already settled.
5. Recommend decisions that still need approval.
6. Reclassify readiness as `ready`, `not_ready`, or `borderline`.
7. Repair `docs/pie/index.md` from authoritative artifacts.

## `/pie:decision <description>`

Manually record, affirm, reject, or override an intent-level decision.

Use it when:

- the human explicitly wants to record a decision;
- a decision was made outside the current agent flow;
- the user wants to accept, reject, or override a recommendation;
- evidence is inconclusive but the user chooses a direction.

Decision records should separate source from status:

```md
### Decision: Short title
- **Decision ID:** DEC-<INTENT-SLUG>-001
- **Decision:** The decision.
- **Rationale:** Why this decision is appropriate.
- **Source:** clarification_response, spike_finding, explicit_user_decision, delivery_feedback, or external_input.
- **Impact:** What changes because of this decision.
- **Status:** proposed, accepted, rejected, or superseded.
```

Normal clarification and distillation should record settled decisions automatically.

## `/pie:baseline`

Optional. Preview or explicitly generate a Delivery Baseline without starting delivery.

Run the readiness gate first:

- `ready`: create or refresh the baseline.
- `not_ready`: stop with blockers.
- `borderline`: ask the user before baselining.

If baselining proceeds:

- update `docs/pie/<intent>/baseline.md`;
- create or refresh the next immutable snapshot under `docs/pie/<intent>/baselines/`;
- repair `docs/pie/index.md`.

This command does not create a Delivery Ask.

## `/pie:implement`

Start direct implementation.

Behavior:

1. Load project context and the explicit or session-selected intent.
2. Run the readiness gate.
3. If `not_ready`, report blockers and stop.
4. If `borderline`, name the judgment call and ask the user before proceeding.
5. If proceeding, create or refresh `baseline.md`.
6. Create immutable baseline revision under `baselines/`.
7. Create direct implementation ask under `asks/`.
8. Mark intent `in_delivery`.
9. Implement from the baseline revision.

Do not invent missing intent to begin implementation.

## `/pie:export <adapter>`

Export to a downstream delivery framework.

Current adapters:

```text
/pie:export speckit
/pie:export lid
```

Behavior:

1. Load project context and the explicit or session-selected intent.
2. Run the readiness gate.
3. If `not_ready`, report blockers and stop.
4. If `borderline`, name the judgment call and ask the user before proceeding.
5. If proceeding, create or refresh `baseline.md`.
6. Create immutable baseline revision under `baselines/`.
7. Create export ask under `asks/`.
8. Load the adapter spec.
9. Write the seed under `docs/pie/<intent>/exports/`.
10. Include a `PIE Origin` block.
11. Mark intent `in_delivery`.

Repeated export to the same adapter should default to updating the known downstream target while still creating a new ask record.

## `/pie:feedback <description>`

Use when direct implementation or downstream delivery reveals learning that may change intent.

Behavior:

1. Identify the intent from an ask ID, explicit context, or session selection. If unclear, ask.
2. Record feedback source lineage when known: ask ID, baseline ID, framework, and downstream target.
3. Classify feedback into one of:
   - routine delivery detail;
   - intent-impacting feedback;
   - ambiguous feedback.
4. Routine delivery detail: summarize briefly and do not churn artifacts.
5. Intent-impacting feedback: update the intent, readiness, derived index, and downstream impact.
6. Ambiguous feedback: ask the user whether to treat it as routine delivery detail or material feedback that reopens intent discovery.
7. Recommend clarification, spike, baseline revision, seed regeneration, or project update as needed.
