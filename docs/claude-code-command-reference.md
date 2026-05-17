# PIE Command Reference

This document defines the Claude Code command model for PIE.

## Durable State And Spike Work

Commands operate on durable artifacts, not chat memory. Spike working code is kept outside `docs/`.

```text
docs/pie/
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
| `/pie:init` | Initialize PIE structure and durable state. |
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
docs/pie/index.md
```

Existing repository rules should be preserved.

`/pie:init` also enforces spike isolation by updating applicable ignore/config files:

- `.gitignore`: add `spikes/`;
- `.eslintignore`: add `spikes/` and `docs/pie/` when ESLint ignore files are used;
- `.npmignore`: add `spikes/` and `docs/pie/` when the repository is an npm package or already has `.npmignore`;
- equivalent tooling excludes for detected lint, test, build, or package-publish systems.

Existing rules and comments must be preserved. Missing PIE entries should be appended under a short `# PIE` section.

## `/pie:intent`

Creates, lists, or switches intents.

Examples:

```text
/pie:intent stock-screener "Build a stock screener based on VCP."
/pie:intent
/pie:intent stock-screener
```

When creating a new intent, the agent should:

1. create `docs/pie/<intent>/intent.md`;
2. register it in `docs/pie/index.md`;
3. set it as active;
4. assign a stable `intent_id` such as `PIE-INTENT-STOCK-SCREENER`;
5. assess maturity;
6. ask focused clarification questions when needed;
7. suggest spikes when empirical evidence is required.

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

1. load active intent;
2. run readiness check;
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

1. load active intent;
2. run readiness check;
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
