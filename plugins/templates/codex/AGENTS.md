# AGENTS.md - PIE Guidance for Codex

This project uses Progressive Intent Engineering (PIE) to decide when work is ready to implement and when it needs clarification, decisions, or spikes first.

## PIE Operating Policy

When the user asks for work that may have unclear intent, Codex should:

- assess whether the intent is ready for delivery before implementing;
- clarify only material ambiguity;
- make reasonable choices for local, reversible details;
- preserve material learning in durable files, not only chat history;
- update `docs/pie/index.md` whenever active context or readiness changes;
- apply the Convergence Rule automatically when a clarification answer, explicit user decision, or clearly settled conclusion resolves material ambiguity or changes intent;
- use spikes for empirical uncertainty;
- keep spike code outside `docs/` under top-level `spikes/<spike>/`;
- route feedback back to PIE only when implementation changes intent.

## Durable PIE State

PIE records live under:

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
```

Spike working code lives under:

```text
spikes/
  <spike>/
```

Use short, lowercase, hyphenated names for intents and spikes.

## Prompt Commands

Codex should treat these prompt patterns as PIE workflow commands:

```text
PIE init
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

1. create or update `docs/pie/<intent>/intent.md`;
2. register the intent in `docs/pie/index.md`;
3. set it as active unless the user says otherwise;
4. assess readiness;
5. ask only material clarifying questions;
6. suggest a spike when evidence is needed.

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

## Distillation Behavior

`PIE distill` should synthesize:

- completed or active spike findings;
- long or branched discovery conversations;
- accumulated evidence;
- explicit user checkpoint requests.

Distillation should update durable artifacts, record settled decisions, recommend unresolved decisions, and update readiness status.

## Delivery Behavior

`PIE implement` should:

1. load the active intent;
2. run the readiness check;
3. create or refresh `baseline.md`;
4. mark the intent `in_delivery`;
5. implement from the baseline.

`PIE export <adapter>` should do the same readiness and baseline work, then write a downstream seed under:

```text
docs/pie/<intent>/exports/
```

Supported adapters:

- `speckit` for [Spec Kit](https://github.com/github/spec-kit)
- `lid` for [LID](https://github.com/jszmajda/lid)

If readiness fails, Codex should stop and report blockers instead of inventing missing intent.
