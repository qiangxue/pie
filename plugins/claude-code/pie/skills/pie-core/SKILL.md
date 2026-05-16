---
name: pie-core
description: Apply Progressive Intent Engineering when work has unclear intent, spikes, durable PIE state, delivery-baseline needs, or feedback that may change intent.
---

# PIE Core

Use Progressive Intent Engineering as an upstream operating policy for software work whose intent may not be implementation-ready.

## Default Behavior

1. Assess whether the user's intent is delivery-ready before designing or coding.
2. Clarify only material ambiguity: product semantics, architecture, boundaries, security, compliance, operability, reliability, cost, performance, scale, external contracts, or material scope.
3. Default to action with isolation for reversible local choices that do not materially affect intent.
4. Draft PIE artifacts yourself; the human reviews, corrects, approves, or rejects them.
5. Maintain `docs/pie/index.md` as durable PIE state. Do not rely on chat history as the source of truth.
6. Apply the Convergence Rule automatically when a clarification answer, explicit user decision, or clearly settled conclusion resolves material ambiguity or changes intent.
7. Treat spikes as child investigations under a parent intent.
8. Use `/pie:distill` for broader synthesis of spike findings, long or branched conversations, accumulated evidence, or explicit checkpoints.
9. Keep spikes tied to named uncertainties and isolated from production tooling.
10. During `/pie:init`, enforce spike isolation in repository ignore, lint, test, and package-publish configuration before spike code is created.
11. Garbage-collect spike work by disposition: discard, distill, continue isolated, promote, or abandon.
12. Generate or refresh the Delivery Baseline automatically when `/pie:implement` or `/pie:export <adapter>` begins delivery.
13. Route delivery feedback back to PIE only when implementation changes the intended work.

## Artifact Defaults

Use per-intent folders and a durable index:

```text
docs/pie/
  index.md
  <intent>/
    intent.md
    baseline.md
    spikes/
      <spike>/
        spike.md
```

Every PIE command must read and update `docs/pie/index.md` when active context, status, baseline state, or spike state changes.

`/pie:init` must add or verify isolation excludes:

- `.gitignore`: `spikes/`
- `.eslintignore`: `spikes/` and `docs/pie/`, when present or when ESLint ignore files are used
- `.npmignore`: `spikes/` and `docs/pie/`, when present or when the repository is an npm package
- equivalent excludes for detected tooling such as Biome, Rome, Prettier, Stylelint, test runners, package publishing, or language-specific lint/build systems

Preserve existing ignore entries and comments. Append missing entries under a short `# PIE` section. `/pie:spike` should verify isolation before creating or running spike code.

Each intent and spike should carry lightweight frontmatter metadata: `type`, `name`, `status`, `created_at`, `updated_at`, and relationship fields where relevant.

Small clear tasks may move quickly to `/pie:implement` or `/pie:export <adapter>`, but the delivery command still creates or refreshes the baseline if needed. Moderate ambiguity usually needs an intent plus decisions. Empirical uncertainty needs one or more spikes.

Use `/pie:intent` with no arguments to list intents and active context. Use `/pie:intent <name>` to switch active intent. Use `/pie:spike` and `/pie:spike <name>` to list or select child spikes.

Clarification answers should auto-trigger Convergence. When the user answers a material clarifying question, immediately update the active intent and index if the answer resolves a tracked ambiguity, creates or confirms a decision, changes current understanding, or affects readiness.

`/pie:distill` should synthesize broader context and automatically record decisions already settled by accumulated conversation or accepted spike findings. It should recommend but not mark as accepted any decision that still requires human approval. `/pie:decision <description>` is for manual recording, affirmation, override, or decisions made outside the normal PIE flow.

## Ready For Delivery

Create or refresh a Delivery Baseline only when:

- The goal is clear enough to implement.
- Material decisions have been made or explicitly deferred as non-blocking.
- Key constraints and non-negotiables are known.
- Success criteria are understandable.
- Downstream delivery can proceed without silently inventing intent.
- For existing systems, prerequisite system preparation has been assessed and separated where needed.

`/pie:baseline` may preview or explicitly generate a baseline, but it is not required in the normal flow. `/pie:implement` and `/pie:export <adapter>` should generate or refresh the baseline automatically after the readiness check passes.

Export adapters live under `${CLAUDE_PLUGIN_ROOT}/adapters/`. `/pie:export speckit` writes `docs/pie/<intent>/exports/speckit-seed.md` for [Spec Kit](https://github.com/github/spec-kit); `/pie:export lid` writes `docs/pie/<intent>/exports/lid-seed.md` for [LID](https://github.com/jszmajda/lid). Adapter output is a downstream seed, not a replacement for the downstream workflow.
