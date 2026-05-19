# Contributing

Thanks for helping improve Progressive Intent Engineering.

This repository contains:

- the PIE framework documentation;
- examples for learning and adoption;
- a Claude Code plugin that operationalizes PIE through skills, slash commands, templates, and export adapters;
- a Codex `AGENTS.md` template for prompt-driven PIE workflows.

PIE is intentionally framework-neutral. Tool integrations should help agents produce durable intent artifacts and downstream seeds without absorbing downstream delivery frameworks.

## Repository Layout

```text
docs/
  framework-specification.md     # definitive framework specification
  claude-code-command-reference.md # Claude Code command model
  codex-guide.md                 # Codex usage guide
  greenfield-example.md
  brownfield-example.md

plugins/claude-code/pie/
  .claude-plugin/plugin.json
  README.md
  commands/
  adapters/
  skills/

plugins/templates/
  claude-code/
    AGENTS.md
    CLAUDE.md
  codex/
    AGENTS.md

.claude-plugin/marketplace.json
  Claude Code marketplace definition.
```

## Development Environment

This repository is documentation and plugin metadata. It has no runtime language dependency and no package installation step is required.

Use ordinary shell tools for validation. If future changes add generated assets or tooling, document those requirements here and keep them minimal.

## Editing Rules

- Keep the main introduction approachable.
- Put detailed framework semantics in `docs/framework-specification.md`.
- Put Claude Code command semantics in `docs/claude-code-command-reference.md`.
- Put Codex prompt workflow guidance in `docs/codex-guide.md` and `plugins/templates/codex/AGENTS.md`.
- Prefer durable artifact behavior over chat-memory assumptions.
- Keep `docs/pie/project.md` as project-level context, not an intent or delivery artifact.
- Treat `project.md`, `intent.md`, `spike.md`, baseline files, ask records, and export seeds as authoritative; `index.md` is derived.
- Keep active intent and active spike session-local, not in shared durable state.
- Assess intents against the Project Goal before delivery.
- Define material ambiguity by whether different answers would produce a meaningfully different Delivery Baseline.
- Do not add commands that are not part of the canonical PIE command model unless the Claude Code command reference is updated first.
- Keep `/pie:baseline` optional; `/pie:implement` and `/pie:export <adapter>` should auto-create or refresh baselines after readiness is `ready` or confirmed `borderline`.
- Keep `/pie:decision` as manual record/affirm/reject/override. Explicit clarification answers should auto-trigger Convergence; inferred or project-shifting decisions require confirmation.
- Keep `/pie:distill` for spike findings, long or branched discovery, accumulated evidence, or explicit checkpoints.

## Canonical Command Set

The Claude Code plugin currently supports:

```text
/pie:init
/pie:project
/pie:intent
/pie:spike
/pie:distill
/pie:decision
/pie:implement
/pie:export
/pie:feedback
/pie:baseline
```

Do not reintroduce removed aliases such as `/pie:assess`, `/pie:discover`, `/pie:converge`, `/pie:status`, `/pie:export-speckit`, or `/pie:export-lid`.

## Adding or Changing Commands

Command files live in:

```text
plugins/claude-code/pie/commands/
```

Each command file must:

- be Markdown;
- start with frontmatter;
- include a `description`;
- describe durable artifact updates;
- avoid relying on chat history as the source of truth.

After changing commands, update:

- [plugins/claude-code/pie/README.md](plugins/claude-code/pie/README.md)
- [plugins/templates/claude-code/AGENTS.md](plugins/templates/claude-code/AGENTS.md)
- [plugins/templates/claude-code/CLAUDE.md](plugins/templates/claude-code/CLAUDE.md), if command names changed
- [docs/claude-code-command-reference.md](docs/claude-code-command-reference.md)
- relevant examples in [docs/](docs/)

## Adding Export Adapters

Adapter specs live in:

```text
plugins/claude-code/pie/adapters/
```

An adapter should:

- read from the immutable baseline revision under `docs/pie/<intent>/baselines/`;
- write under `docs/pie/<intent>/exports/`;
- repair the derived `docs/pie/index.md`;
- preserve the PIE Origin block with intent ID, baseline ID, and ask ID;
- produce a downstream seed, not a full downstream workflow;
- stop and reopen PIE if export reveals delivery-critical missing intent.

After adding an adapter, update `/pie:export`, plugin docs, templates, command reference, and examples.

## Validation Checklist

Before publishing or opening a pull request:

```sh
node -e "JSON.parse(require('fs').readFileSync('.claude-plugin/marketplace.json','utf8')); JSON.parse(require('fs').readFileSync('plugins/claude-code/pie/.claude-plugin/plugin.json','utf8'));"
```

Also check:

- command files have frontmatter descriptions;
- obsolete command names are not referenced outside this file;
- examples use the current command model;
- plugin metadata versions match;
- any new tooling requirements are documented in this file.

## Contribution Flow

1. Open an issue or discussion for substantial framework or command-model changes.
2. Keep pull requests focused.
3. Update examples when behavior changes.
4. Explain whether a change affects framework semantics, plugin behavior, or documentation only.
5. Run the validation checklist before submitting.

## Release Notes

When cutting a release:

1. Update `.claude-plugin/marketplace.json`.
2. Update `plugins/claude-code/pie/.claude-plugin/plugin.json`.
3. Update plugin and root README files if installation, commands, or adapters changed.
4. Confirm examples still match the released command model.
