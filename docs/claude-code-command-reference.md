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
4. assess maturity;
5. ask focused clarification questions when needed;
6. suggest spikes when empirical evidence is required.

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
4. mark intent `in_delivery`;
5. implement from the baseline.

If not ready, the command should fail with blockers.

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
4. load the adapter spec;
5. write under `docs/pie/<intent>/exports/`;
6. mark intent `in_delivery`.

## `/pie:feedback`

Reconciles delivery feedback back into PIE.

Use it when delivery reveals:

- a challenged assumption;
- new material ambiguity;
- new empirical uncertainty;
- a preparation gap;
- intent-changing downstream framework feedback.

Routine implementation details should not churn PIE artifacts.

## `/pie:baseline`

Optional command for previewing or explicitly generating a Delivery Baseline without starting implementation or export.

The normal workflow does not require it because `/pie:implement` and `/pie:export <adapter>` auto-create or refresh the baseline after readiness passes.
