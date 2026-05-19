# AGENTS.md - PIE Guidance for Codex

Use Progressive Intent Engineering (PIE) when work is not clearly ready to implement.

## PIE Policy

Codex should:

- assess readiness before implementation or export;
- clarify only material ambiguity, meaning ambiguity where different answers would produce a meaningfully different Delivery Baseline;
- make reasonable choices for local reversible details;
- treat `project.md`, `intent.md`, `spike.md`, baseline files, ask records, and export seeds as authoritative;
- treat `docs/pie/index.md` as a derived registry that can be repaired from artifacts;
- keep active intent and active spike session-local, not as shared durable state;
- assess new intents against the Project Goal;
- use spikes when evidence is needed;
- record explicit settled decisions automatically when clarification or distillation resolves ambiguity;
- ask before recording inferred decisions or project-framing shifts as accepted;
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

`PIE init` must exclude `spikes/` from Git, and must exclude both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. Preserve existing ignore rules and append missing entries under `# PIE`. Do not add `docs/pie/` to `.gitignore` by default; it is durable PIE state.

## Prompt Commands

These are prompt patterns, not shell commands:

```text
PIE init
PIE project
PIE intent <name>: <description>
PIE list intents
PIE switch intent <name>
PIE spike <name>: <question>
PIE distill
PIE decision: <description>
PIE implement
PIE export speckit
PIE export lid
PIE feedback: <description>
PIE baseline
```

## Readiness Gate

Before `PIE implement`, `PIE export <adapter>`, or `PIE baseline`, confirm:

- the goal is clear enough to implement;
- the intent aligns with the Project Goal or drift is resolved;
- relevant guardrails are reflected;
- material decisions are made or explicitly deferred;
- constraints and success criteria are known;
- no active, completed-but-undistilled, or blocking spike blocks delivery;
- brownfield prerequisite system preparation has been assessed and represented in the intent or baseline.

Classify readiness as `ready`, `not_ready`, or `borderline`. If `not_ready`, stop with blockers. If `borderline`, name the judgment call and ask the user before baselining, implementing, or exporting.

## Behavior Notes

- `PIE init` creates or confirms `docs/pie/project.md` and derived `docs/pie/index.md`.
- `PIE intent <name>: <description>` creates or updates an intent, assesses Project Goal alignment, and asks only material clarification questions.
- Explicit clarification answers should update durable intent artifacts immediately. Inferred decisions require confirmation before being recorded as accepted.
- `PIE spike <name>` creates or continues a focused investigation under an explicit or session-selected intent.
- `PIE distill` folds spike findings or long discovery into the parent intent.
- `PIE implement` and `PIE export <adapter>` create a baseline revision and delivery ask before delivery.
- `PIE feedback: ...` classifies feedback as routine delivery detail, intent-impacting feedback, or ambiguous feedback.
