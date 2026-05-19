---
name: pie-core
description: Apply Progressive Intent Engineering when work has unclear intent, project-goal alignment concerns, spikes, delivery-baseline needs, or intent-changing feedback.
---

# PIE Core

Use Progressive Intent Engineering as an upstream operating policy for software work whose intent may not be delivery-ready.

## Operating Model

PIE answers:

```text
Is this ready to deliver?
If yes, what stable baseline should delivery use?
If no, what question, decision, evidence, or feedback is missing?
```

Keep the model simple:

```text
Project Goal -> Intent -> Spike/Decision -> Delivery Baseline -> Delivery Ask -> Delivery -> Feedback
```

PIE does not own downstream task decomposition. Specs, plans, tasks, implementation, tests, [Spec Kit](https://github.com/github/spec-kit), and [LID](https://github.com/jszmajda/lid) stay downstream.

## Default Behavior

- Assess readiness before coding or exporting.
- Clarify only material ambiguity: an ambiguity is material when different plausible answers would produce a meaningfully different Delivery Baseline.
- Make reasonable choices for local reversible details.
- Treat `project.md`, `intent.md`, `spike.md`, baseline files, ask records, and export seeds as authoritative. Treat `docs/pie/index.md` as a derived registry that can be repaired from artifacts.
- Keep active intent and active spike session-local. Do not store a shared active cursor in `docs/pie/index.md`.
- Treat the Project Goal as the guardrail for intents, not as an intent or delivery artifact.
- Assess new intents against the Project Goal and surface project drift.
- Apply Convergence automatically for explicit answers, explicit user decisions, accepted options, and accepted pending recommendations.
- Ask before recording inferred decisions or project-framing shifts as accepted.
- Use spikes for empirical uncertainty.
- Use `/pie:distill` for spike findings, long discovery conversations, accumulated evidence, or explicit checkpoints.
- Generate or refresh a Delivery Baseline automatically when `/pie:implement` or `/pie:export <adapter>` begins delivery.
- Preserve traceability from intent ID to baseline revision to delivery ask to downstream target.
- Route delivery feedback back to PIE only when it changes intent.

## Durable State

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

Spike-only code belongs in `spikes/<spike>/`, outside `docs/`. `/pie:init` must exclude `spikes/` from Git, and must exclude both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. `docs/pie/` is durable PIE state and should normally be committed.

## Readiness Gate

Classify readiness before creating or refreshing a Delivery Baseline:

- `ready`: proceed.
- `not_ready`: stop with blockers.
- `borderline`: name the judgment call and ask the user before proceeding.

Ready means:

- the goal is clear enough to implement;
- the intent aligns with the Project Goal or project drift has been resolved;
- relevant guardrails are reflected;
- material decisions are made or deferred as non-blocking;
- constraints and success criteria are known;
- no active, completed-but-undistilled, or blocking spike blocks delivery;
- delivery can proceed without inventing intent;
- brownfield prerequisite system preparation has been assessed and represented in the intent or baseline.

If readiness fails or is borderline, report blockers or judgment calls and recommend clarification, spike work, distillation, or project-goal review.

## Command Behavior

- `/pie:init`: create project context, derived index, guidance files, and spike isolation.
- `/pie:project`: show or update project goal and guardrails.
- `/pie:intent`: create, list, or select intents for the current session; new intents must be assessed against the Project Goal.
- `/pie:spike`: create, list, select, or continue spikes under an explicit or session-selected intent.
- `/pie:distill`: fold spike findings or long discovery context into durable intent updates.
- `/pie:decision`: manual decision record, affirmation, rejection, or override.
- `/pie:implement`: run the readiness gate, create baseline revision and delivery ask, then direct implementation.
- `/pie:export <adapter>`: run the readiness gate, create baseline revision and delivery ask, then downstream seed.
- `/pie:feedback`: reconcile intent-changing delivery feedback back into PIE.
- `/pie:baseline`: optional baseline preview without delivery.

Feedback triage has three outcomes: routine delivery detail, intent-impacting feedback, or ambiguous feedback. Ask the user before reopening PIE for ambiguous feedback.

Export adapters live under `${CLAUDE_PLUGIN_ROOT}/adapters/`. `/pie:export speckit` writes a [Spec Kit](https://github.com/github/spec-kit) seed; `/pie:export lid` writes an [LID](https://github.com/jszmajda/lid) seed. Adapter output must include a `PIE Origin` block with the PIE Intent ID, Delivery Baseline ID, and Delivery Ask ID.
