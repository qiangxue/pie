# AGENTS.md - PIE Project Guidance

This repository uses Progressive Intent Engineering (PIE) when work is not clearly ready to implement.

## PIE Policy

Agents should:

- assess readiness before implementation or export;
- clarify only material ambiguity;
- make reasonable choices for local reversible details;
- keep `docs/pie/project.md` and `docs/pie/index.md` as durable state;
- assess new intents against the Project Goal;
- use spikes when evidence is needed;
- record settled decisions automatically when clarification or distillation resolves ambiguity;
- generate Delivery Baselines only after the readiness gate passes;
- preserve traceability from intent ID to baseline revision to delivery ask to downstream target;
- send feedback back to PIE only when delivery changes intent.

PIE does not own downstream task decomposition. Specs, plans, tasks, implementation, tests, [Spec Kit](https://github.com/github/spec-kit), and [LID](https://github.com/jszmajda/lid) stay downstream.

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

Spike-only code belongs in `spikes/<spike>/`, outside `docs/`.

`/pie:init` must exclude `spikes/` from Git, and must exclude both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. Preserve existing ignore rules and append missing entries under a short `# PIE` section. Do not add `docs/pie/` to `.gitignore` by default; it is durable PIE state.

## Commands

Use the installed PIE plugin commands:

- `/pie:init`
- `/pie:project`
- `/pie:intent`
- `/pie:spike`
- `/pie:distill`
- `/pie:decision`
- `/pie:implement`
- `/pie:export`
- `/pie:feedback`
- `/pie:baseline`

Start intent work with:

```text
/pie:intent <name> <description>
```

Use `/pie:project` to inspect or update the Project Goal. Use `/pie:intent` to list intents and `/pie:intent <name>` to switch active context.

## Readiness Gate

Before `/pie:implement`, `/pie:export <adapter>`, or `/pie:baseline`, confirm:

- the goal is clear enough to implement;
- the intent aligns with the Project Goal or drift is resolved;
- relevant guardrails are reflected;
- material decisions are made or explicitly deferred;
- constraints and success criteria are known;
- no active or undistilled spike blocks delivery;
- brownfield preparation has been assessed where needed.

If readiness fails, report blockers and recommend clarification, spike work, distillation, or project-goal review.

## Spikes

Use `/pie:spike <name>` for focused evidence gathering under the active intent. Keep experimental code in `spikes/<name>/`. Capture findings in `docs/pie/<intent>/spikes/<name>/spike.md`, then use `/pie:distill` to fold findings back into the parent intent.

## Delivery And Feedback

`/pie:implement` and `/pie:export <adapter>` create:

- an immutable baseline snapshot under `docs/pie/<intent>/baselines/`;
- a delivery ask under `docs/pie/<intent>/asks/`.

Preserve:

```text
PIE Intent ID -> Delivery Baseline ID -> Delivery Ask ID -> Downstream Target ID
```

Use `/pie:feedback <description>` only for delivery findings that change intent. Routine implementation details stay in delivery.
