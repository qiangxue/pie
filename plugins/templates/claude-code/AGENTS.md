# AGENTS.md - PIE Project Guidance

This repository uses Progressive Intent Engineering (PIE) to decide when work is ready to implement and when it needs clarification, decisions, or spikes first.

## Global PIE Behavior

Agents should:

- assess whether intent is delivery-ready before implementing;
- clarify only material ambiguity;
- default to action with isolation for reversible local choices;
- draft PIE artifacts rather than asking humans to write them;
- maintain `docs/pie/index.md` as durable PIE state;
- apply the Convergence Rule automatically when a clarification answer, explicit user decision, or clearly settled conclusion resolves material ambiguity or changes intent;
- treat spikes as child investigations under a parent intent;
- use `/pie:distill` for broader synthesis of spike findings, long or branched discovery conversations, accumulated evidence, or explicit checkpoints;
- automatically record decisions that are already settled during distillation;
- recommend but do not silently accept decisions that still require human approval;
- keep spikes tied to named uncertainties;
- isolate spike work from production tooling;
- garbage-collect spike work by disposition;
- route delivery feedback back to PIE only when implementation changes intent.

## Durable State And Spike Work

PIE artifacts live under `docs/pie/`. Spike working code lives under top-level `spikes/`:

```text
docs/pie/
  index.md
  <intent>/
    intent.md
    baseline.md
    spikes/
      <spike>/
        spike.md
spikes/
  <spike>/
```

`docs/pie/index.md` is the registry of durable PIE state. It tracks active intent, active spike, intent statuses, baseline status, open spikes, blockers, and artifact links.

Use these default filenames:

- `intent.md` for the parent intent.
- `baseline.md` for delivery-ready intent.
- `docs/pie/<intent>/spikes/<spike>/spike.md` for child empirical investigations.
- `preparation-baseline.md` only when existing-system preparation is needed before or alongside new intent.

Use top-level `spikes/<spike>/` for spike-only code, fixtures, scripts, notes, and prototypes. Do not put experimental code under `docs/`.

Use per-intent folders by default. Keep intent and spike names short, lowercase, and hyphenated.

## Command Model

Use the PIE plugin commands when available in Claude Code:

- `/pie:init`
- `/pie:intent`
- `/pie:spike`
- `/pie:distill`
- `/pie:decision`
- `/pie:implement`
- `/pie:export`
- `/pie:feedback`
- `/pie:baseline`

Start new PIE work with:

```text
/pie:intent <name> <description>
```

Use `/pie:intent` with no arguments to list intents and active context. Use `/pie:intent <name>` to switch active intent. Each parallel intent should have its own folder and index entry.

When the user answers a material clarifying question, update the active intent and index immediately if the answer resolves a tracked ambiguity, creates or confirms a decision, changes current understanding, or affects readiness. Do not require `/pie:distill` for ordinary one-question/one-answer convergence.

Use `/pie:spike <name>` to create or select a child spike under the active intent. Use `/pie:distill` to distill active spike findings, long or branched discovery, multiple partial conclusions, or explicit checkpoints back into durable artifacts. Distillation should record settled decisions automatically and separately list recommended decisions that still need approval.

Use `/pie:decision <description>` for manual recording, affirmation, override, or decisions made outside the PIE flow. It should not be required after every distillation.

`/pie:baseline` is optional. `/pie:implement` and `/pie:export <adapter>` should run the readiness check and generate or refresh the Delivery Baseline automatically.

PIE export adapters write downstream seeds under `docs/pie/<intent>/exports/`. Supported adapters:

- `/pie:export speckit` -> `speckit-seed.md` for [Spec Kit](https://github.com/github/spec-kit)
- `/pie:export lid` -> `lid-seed.md` for [LID](https://github.com/jszmajda/lid)

Adapters produce seeds for downstream workflows. They should not replace [Spec Kit](https://github.com/github/spec-kit) or [LID](https://github.com/jszmajda/lid).

For direct implementation, the agent may automatically apply feedback behavior when it discovers intent-changing information. For downstream delivery frameworks, use `/pie:feedback <description>` explicitly to reconcile findings back into PIE.

## Intent Statuses

Use these statuses in intent metadata and the index:

- `discovering`
- `needs_clarification`
- `needs_spike`
- `ready_for_delivery`
- `in_delivery`
- `reopened`
- `completed`

## Spike Statuses

Use these statuses in spike metadata and the index:

- `proposed`
- `active`
- `completed`
- `distilled`
- `discarded`
- `promoted`

## Spikes

Each spike must resolve a named uncertainty and belong to a parent intent.

Spike code belongs under top-level `spikes/<spike>/`. Capture material findings in the spike artifact and then distill them into the parent intent.

If a spike must touch real project files to gather evidence, record those paths in `spike.md` with the reason and disposition. Do not treat spike code as production code unless it is explicitly promoted after distillation or delivery planning.

Spike branches may be used for deeper investigations. Summarize findings into the spike artifact before closing or archiving the branch.

Curated research experiments may be committed only when they provide durable decision value. Keep them isolated under `research/`, `experiments/curated/`, or an equivalent folder, and exclude them from production paths and default CI unless intentionally integrated.

## Delivery Paths

When intent is ready, choose the downstream path that fits the work:

- `/pie:implement` for direct implementation;
- `/pie:export speckit` when [Spec Kit](https://github.com/github/spec-kit) delivery is useful;
- `/pie:export lid` when [LID](https://github.com/jszmajda/lid) traceability matters;
- preparation delivery when the existing system must be readied first;
- more clarification, decisions, or spikes when blocking uncertainty remains.
