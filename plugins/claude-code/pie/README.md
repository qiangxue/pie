# PIE for Claude Code

This plugin packages Progressive Intent Engineering for Claude Code.

For the framework overview and specification, see:

- [README.md](../../../README.md)
- [docs/framework-specification.md](../../../docs/framework-specification.md)
- [docs/claude-code-command-reference.md](../../../docs/claude-code-command-reference.md)

## What It Provides

- Layer 1: `pie-core`, a compact skill that teaches Claude the default PIE operating policy.
- Layer 2: shared templates under `plugins/templates/claude-code/`, including `AGENTS.md` and a `CLAUDE.md` shim for Claude Code.
- Layer 3: slash commands under `commands/` for repeatable PIE workflows.

## Install From This Repository

Add `github.com/qiangxue/pie` as a marketplace and install the plugin:

```text
/plugin marketplace add qiangxue/pie
/plugin install pie@pie
```

Restart Claude Code after installation.

## Commands

Claude Code normally exposes plugin commands with the plugin namespace:

- `/pie:init`: initialize project guidance, Project Goal, `docs/pie/`, and `docs/pie/index.md`.
- `/pie:project`: display or update the Project Goal, guardrails, and shared principles.
- `/pie:intent <name> <description>`: create a new intent and assess maturity.
- `/pie:intent`: list intents and active context.
- `/pie:intent <name>`: switch active intent and summarize durable state.
- `/pie:spike <name>`: create, select, or continue a spike under the active intent.
- `/pie:spike`: list spikes for the active intent.
- `/pie:distill`: synthesize spike findings, long discovery conversations, or explicit checkpoints into the active intent; record settled decisions and recommend unresolved ones.
- `/pie:decision <description>`: manually record, affirm, or override an intent-level decision.
- `/pie:implement`: readiness-check, auto-baseline, and begin direct implementation.
- `/pie:export <adapter>`: readiness-check, auto-baseline, and export to [Spec Kit](https://github.com/github/spec-kit), [LID](https://github.com/jszmajda/lid), or another adapter.
- `/pie:feedback <description>`: reconcile delivery feedback back into PIE.
- `/pie:baseline`: optional baseline preview or explicit generation without delivery.

Claude Code plugin commands are namespaced as `/plugin-name:command-name`. For this plugin, use `/pie:intent`, `/pie:spike`, `/pie:implement`, and so on.

## Export Adapters

Adapter specs live in `adapters/`:

- [`speckit`](https://github.com/github/spec-kit): `/pie:export speckit` writes `docs/pie/<intent>/exports/speckit-seed.md`.
- [`lid`](https://github.com/jszmajda/lid): `/pie:export lid` writes `docs/pie/<intent>/exports/lid-seed.md`.

PIE adapters produce downstream seeds. They do not replace the downstream framework workflow.

Delivery commands preserve PIE traceability:

```text
PIE Intent ID -> Delivery Baseline ID -> Delivery Ask ID -> Downstream Target ID
```

`/pie:implement` and `/pie:export <adapter>` create immutable baseline snapshots under `docs/pie/<intent>/baselines/` and ask records under `docs/pie/<intent>/asks/`. Adapter output includes a `PIE Origin` block with the intent ID, baseline ID, and ask ID.
