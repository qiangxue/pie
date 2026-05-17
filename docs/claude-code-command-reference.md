# PIE Command Reference

This document defines the Claude Code command model for PIE.

## Durable State And Spike Work

Commands operate on durable artifacts, not chat memory. Spike working code is kept outside `docs/`.

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
    preparation-baseline.md
    exports/
    spikes/
      <spike>/
        spike.md
spikes/
  <spike>/
```

## Commands

| Command | Purpose |
|---|---|
| `/pie:init` | Initialize PIE structure, Project Goal, and durable state. |
| `/pie:project` | Display or update project-level goal, guardrails, and shared principles. |
| `/pie:intent <name> <description>` | Create a new intent and assess its maturity. |
| `/pie:intent` | List intents and active context. |
| `/pie:intent <name>` | Switch active intent. |
| `/pie:spike <name>` | Create, inspect, select, or continue a spike under the active intent. |
| `/pie:spike` | List spikes under the active intent. |
| `/pie:distill` | Synthesize spike findings, long conversations, or checkpoints into durable intent updates. |
| `/pie:decision <description>` | Manually record, affirm, reject, or override a decision. |
| `/pie:implement` | Readiness-check, auto-baseline, and begin direct implementation. |
| `/pie:export <adapter>` | Readiness-check, auto-baseline, and export to a downstream adapter. |
| `/pie:feedback <description>` | Reconcile implementation or downstream delivery feedback back into PIE. |
| `/pie:baseline` | Optional baseline preview or explicit generation without delivery. |

## `/pie:init`

Initializes:

```text
AGENTS.md
CLAUDE.md
docs/pie/
docs/pie/project.md
docs/pie/index.md
```

Existing repository rules should be preserved.

For a greenfield repository, `/pie:init` should ask for the high-level Project Goal, clarify only material project-level ambiguity, and create `docs/pie/project.md`.

For a brownfield repository, `/pie:init` should inspect existing durable context where available, including README files, architecture docs, ADRs, product docs, repo structure, and existing agent guidance. It should propose a reconstructed Project Goal and guardrails, then ask the user to confirm or revise them before creating `docs/pie/project.md`.

`/pie:init` also enforces spike isolation by updating applicable ignore/config files:

- `.gitignore`: add `spikes/`;
- `.eslintignore`: add `spikes/` and `docs/pie/` when ESLint ignore files are used;
- `.npmignore`: add `spikes/` and `docs/pie/` when the repository is an npm package or already has `.npmignore`;
- equivalent tooling excludes for detected lint, test, build, or package-publish systems.

Existing rules and comments must be preserved. Missing PIE entries should be appended under a short `# PIE` section.

## `/pie:project`

Displays the current project-level context from `docs/pie/project.md`.

The agent should show:

- Project Goal;
- Project Guardrails;
- Shared Project Principles;
- Current System Understanding, for brownfield projects;
- Known Evolution Themes, when present;
- Open Project-Level Questions, when present.

End with a light invitation to update the Project Goal, guardrails, or shared principles. Do not force an update before continuing normal work.

If `docs/pie/project.md` is missing, recommend `/pie:init` or create it with the same greenfield/brownfield behavior described above.

## `/pie:intent`

Creates, lists, or switches intents.

Examples:

```text
/pie:intent stock-screener "Build a stock screener based on VCP."
/pie:intent
/pie:intent stock-screener
```

When creating a new intent, the agent should:

1. load `docs/pie/project.md`;
2. assess alignment with the Project Goal and guardrails;
3. flag possible project drift if the intent stretches or contradicts the Project Goal;
4. create `docs/pie/<intent>/intent.md`;
5. register it in `docs/pie/index.md`;
6. set it as active;
7. assign a stable `intent_id` such as `PIE-INTENT-STOCK-SCREENER`;
8. assess maturity;
9. ask focused clarification questions when needed;
10. suggest spikes when empirical evidence is required.

If alignment is unclear, ask whether to reframe the intent, update the Project Goal, or treat the work as a separate project. Do not silently let an intent change what the project is for.

Clarification answers should auto-trigger Convergence when they resolve material ambiguity.

## `/pie:spike`

Creates, lists, selects, or continues spikes under the active intent.

Examples:

```text
/pie:spike
/pie:spike scoring-model
```

A spike should define:

- question;
- hypothesis;
- method;
- evaluation;
- decision impact.

Spike-only code, fixtures, scripts, and notes should be created outside `docs/` under:

```text
spikes/<spike>/
```

The `docs/pie/<intent>/spikes/<spike>/spike.md` file is the durable record. The top-level `spikes/<spike>/` directory is the working area. If the spike needs to touch real project files, record those paths and the reason in `spike.md`. Do not treat spike code as production code unless it is explicitly promoted later.

Before creating or running spike code, verify the isolation excludes created by `/pie:init` are present.

## `/pie:distill`

Use for synthesis, not routine one-question clarification.

Use it when:

- a spike has findings;
- a long or branched conversation needs consolidation;
- several partial conclusions accumulated;
- the user wants a durable checkpoint.

The agent should:

- summarize findings;
- update spike and/or intent artifacts;
- record settled decisions;
- recommend unresolved decisions;
- update readiness and the index.

## `/pie:decision`

Manual decision command.

Use it when:

- the human wants to explicitly record a decision;
- a decision was made outside the current agent flow;
- the user wants to accept, reject, or override a recommendation;
- evidence is inconclusive but the user chooses a direction.

## `/pie:implement`

Starts direct implementation.

The agent must:

1. load project context and active intent;
2. run readiness check, including project-goal alignment;
3. create or refresh `baseline.md`;
4. create an immutable baseline snapshot under `docs/pie/<intent>/baselines/`;
5. create a delivery ask record under `docs/pie/<intent>/asks/`;
6. mark intent `in_delivery`;
7. implement from the baseline revision.

If not ready, the command should fail with blockers.

The ask record should use `delivery_mode: direct`, `target_framework: direct`, and a downstream target such as `implementation-session-YYYY-MM-DD`.

## `/pie:export <adapter>`

Exports to a downstream framework.

Current adapters:

```text
/pie:export speckit
/pie:export lid
```

These target [Spec Kit](https://github.com/github/spec-kit) and [LID](https://github.com/jszmajda/lid), respectively.

The agent must:

1. load project context and active intent;
2. run readiness check, including project-goal alignment;
3. create or refresh `baseline.md`;
4. create an immutable baseline snapshot under `docs/pie/<intent>/baselines/`;
5. create a delivery ask record under `docs/pie/<intent>/asks/`;
6. load the adapter spec;
7. write under `docs/pie/<intent>/exports/`;
8. include PIE origin metadata in the export;
9. mark intent `in_delivery`.

When exporting the same intent to the same adapter again, default to updating the known downstream target for that adapter. Still create a new ask record for the new handoff. Create a new downstream target only when the user requests it, the work is materially independent, or the downstream framework requires it.

## `/pie:feedback`

Reconciles delivery feedback back into PIE.

Use it when delivery reveals:

- a challenged assumption;
- new material ambiguity;
- new empirical uncertainty;
- a preparation gap;
- intent-changing downstream framework feedback.

Routine implementation details should not churn PIE artifacts.

When possible, feedback should reference a delivery ask:

```text
/pie:feedback "PIE-ASK-STOCK-SCREENER-SPECKIT-002 exposed an evaluation ambiguity."
```

If no ask ID is supplied, use the active intent's latest ask from `docs/pie/index.md`. The feedback record should retain `ask_id`, `baseline_id`, `downstream_target_id`, and `target_framework` when known.

## `/pie:baseline`

Optional command for previewing or explicitly generating a Delivery Baseline without starting implementation or export.

The normal workflow does not require it because `/pie:implement` and `/pie:export <adapter>` auto-create or refresh the baseline after readiness passes.

When `/pie:baseline` is used explicitly and readiness passes, update `baseline.md` and create or refresh the next immutable snapshot under `docs/pie/<intent>/baselines/`. It does not create a delivery ask because no handoff has started.
