# AGENTS.md - PIE Guidance for Codex

This project uses Progressive Intent Engineering (PIE) to decide when work is ready to implement and when it needs clarification, decisions, or spikes first.

## PIE Operating Policy

When the user asks for work that may have unclear intent, Codex should:

- assess whether the intent is ready for delivery before implementing;
- clarify only material ambiguity;
- make reasonable choices for local, reversible details;
- preserve material learning in durable files, not only chat history;
- maintain `docs/pie/project.md` as project-level context;
- update `docs/pie/index.md` whenever active context or readiness changes;
- treat the Project Goal as the alignment guardrail for intents, not as an intent or delivery artifact;
- assess new intents against the Project Goal and surface project drift;
- apply the Convergence Rule automatically when a clarification answer, explicit user decision, or clearly settled conclusion resolves material ambiguity or changes intent;
- use spikes for empirical uncertainty;
- keep spike code outside `docs/` under top-level `spikes/<spike>/`;
- enforce spike isolation in repository ignore, lint, test, and package-publish configuration during `PIE init`;
- preserve traceability from intent ID to baseline revision to delivery ask to downstream target;
- route feedback back to PIE only when implementation changes intent.

## Durable PIE State

PIE records live under:

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
```

Spike working code lives under:

```text
spikes/
  <spike>/
```

Use short, lowercase, hyphenated names for intents and spikes.

`docs/pie/project.md` records the Project Goal, guardrails, shared principles, and brownfield system context when relevant. The Project Goal does not enter the Discover -> Baseline -> Delivery lifecycle.

`PIE init` must enforce isolation by adding PIE excludes to applicable tooling:

- `.gitignore`: `spikes/`
- `.eslintignore`: `spikes/` and `docs/pie/`, when present or when ESLint ignore files are used
- `.npmignore`: `spikes/` and `docs/pie/`, when present or when the repository is an npm package
- equivalent exclude settings for detected tooling such as Biome, Rome, Prettier, Stylelint, test runners, package publishing, or language-specific lint/build systems

Append missing entries under a short `# PIE` section and preserve existing ignore rules. Spike work should not enter commits, lint/build pipelines, or published packages unless explicitly promoted.

`PIE init` must also create `docs/pie/project.md`. For greenfield projects, ask for the high-level Project Goal and material guardrails. For brownfield projects, inspect existing durable context, propose a reconstructed Project Goal and guardrails, and ask the user to confirm or revise before writing it.

## Prompt Commands

Codex should treat these prompt patterns as PIE workflow commands:

```text
PIE init
PIE project
PIE intent <name>: <description>
PIE list intents
PIE switch intent <name>
PIE spike <name>: <question or purpose>
PIE distill
PIE decision: <description>
PIE implement
PIE export speckit
PIE export lid
PIE feedback: <description>
PIE baseline
```

These are not shell commands. They are repeatable prompt forms that tell Codex which PIE behavior to perform.

## Intent Behavior

`PIE intent <name>: <description>` should:

1. load `docs/pie/project.md`;
2. assess alignment with the Project Goal and guardrails;
3. flag project drift if the intent stretches or contradicts the Project Goal;
4. create or update `docs/pie/<intent>/intent.md`;
5. register the intent in `docs/pie/index.md`;
6. set it as active unless the user says otherwise;
7. assess readiness;
8. ask only material clarifying questions;
9. suggest a spike when evidence is needed.

If alignment is unclear or negative, ask whether to reframe the intent, update the Project Goal, or treat the work as a separate project.

If the user answers a material clarifying question, update the active intent and index immediately when the answer resolves ambiguity, creates or confirms a decision, changes current understanding, or affects readiness.

Do not require `PIE distill` for ordinary one-question clarification.

## Spike Behavior

`PIE spike <name>` should create or continue a child spike under the active intent.

The durable spike record is:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
```

Spike-only code, scripts, fixtures, notes, and prototypes belong under:

```text
spikes/<spike>/
```

If a spike touches real project files, record the touched paths, reason, and disposition in `spike.md`.

Before creating or running spike code, verify the isolation excludes from `PIE init` are present. If not, update the relevant ignore/config files first.

## Distillation Behavior

`PIE distill` should synthesize:

- completed or active spike findings;
- long or branched discovery conversations;
- accumulated evidence;
- explicit user checkpoint requests.

Distillation should update durable artifacts, record settled decisions, recommend unresolved decisions, and update readiness status.

## Delivery Behavior

`PIE implement` should:

1. load project context and the active intent;
2. run the readiness check, including project-goal alignment;
3. create or refresh `baseline.md`;
4. create an immutable baseline snapshot under `docs/pie/<intent>/baselines/`;
5. create a delivery ask record under `docs/pie/<intent>/asks/`;
6. mark the intent `in_delivery`;
7. implement from the baseline revision.

`PIE export <adapter>` should do the same readiness and baseline work, then write a downstream seed under:

```text
docs/pie/<intent>/exports/
```

Delivery must preserve this chain:

```text
PIE Intent ID -> Delivery Baseline ID -> Delivery Ask ID -> Downstream Target ID
```

Adapter output should include a `PIE Origin` block with the PIE Intent ID, Delivery Baseline ID, and Delivery Ask ID. If a downstream target ID is unknown, record a pending or proposed target in the ask and update it when known. Re-exporting the same intent to the same adapter should default to updating the known downstream target while still creating a new ask record.

Supported adapters:

- `speckit` for [Spec Kit](https://github.com/github/spec-kit)
- `lid` for [LID](https://github.com/jszmajda/lid)

If readiness fails, Codex should stop and report blockers instead of inventing missing intent.
