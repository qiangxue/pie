# Progressive Intent Engineering

Coding agents are fast when the work is clear. Real projects rarely start that way.

Most useful software work begins as an ambiguous intent: a rough product direction, a fuzzy workflow improvement, a brownfield change with hidden constraints, or a technical idea that still needs evidence. The user may know the pain, but not the exact behavior, scope, architecture, data model, or success criteria.

That is where many agent projects go wrong:

- the agent starts coding before the problem is stable;
- the agent asks questions, but the answers stay trapped in chat;
- important decisions are made without enough data points;
- exploratory spikes produce useful learning, but the learning is not folded back into the intent;
- implementation discovers something that changes the goal, but the original intent is never updated;
- downstream specs or plans have to invent missing product meaning;
- multiple efforts drift because nobody can tell which intent is ready, blocked, or reopened.

Progressive Intent Engineering (PIE) is a lightweight workflow for maturing ambiguous software intent until it is ready to deliver.

PIE sits before implementation and before downstream delivery frameworks. It helps an agent answer:

```text
Is this work ready to deliver?
If yes, what stable baseline should delivery begin from?
If no, what evidence, decision, or feedback loop is missing?
```

PIE is intentionally small. It does not replace specs, plans, tests, [Spec Kit](https://github.com/github/spec-kit), [LID](https://github.com/jszmajda/lid), or implementation. It prepares better input for them.

## What Makes PIE Different

PIE is not just another intent-driven development wrapper. It assumes intent is often incomplete, unstable, or wrong at first.

PIE gives agents a way to:

- separate vague intent from delivery-ready intent;
- ask only questions that materially change the product, architecture, risk, or delivery path;
- run spikes when a decision needs evidence instead of opinion;
- record decisions, rationale, and impact as durable artifacts;
- create a Delivery Baseline only after blocking ambiguity is resolved;
- feed implementation-stage or downstream-planning discoveries back into the intent when they change what should be built.

The important move is feedback: PIE does not treat intent as frozen once coding starts. If delivery reveals that an assumption was wrong, a success criterion is missing, or an existing system cannot support the desired behavior, PIE reopens the intent and updates the durable record.

## How PIE Works

PIE treats intent as something that may need to mature before delivery begins.

The basic loop is:

```text
project goal
-> unclear or new intent
-> clarify material ambiguity
-> gather data points through spikes when discussion is not enough
-> record decisions, rationale, and impact durably
-> create a Delivery Baseline when ready
-> implement directly or export downstream
-> feed back implementation or delivery discoveries that change intent
```

### Project Goal

The Project Goal is the project-level answer to:

```text
What is this project fundamentally trying to accomplish?
```

It is broad, durable, and used as a guardrail for individual intents. It is not an intent, spike, baseline, or delivery artifact.

For a greenfield project, PIE helps define the Project Goal during initialization. For a brownfield project, PIE reconstructs a candidate goal from existing durable context, then asks the user to confirm or revise it.

The Project Goal lives at:

```text
docs/pie/project.md
```

### Intent

An intent is the durable record of what you are trying to build, what is currently understood, what remains ambiguous, and what decisions have already been made.

Example:

```text
stock-screener
```

with a goal like:

```text
Build a screener that ranks promising pre-breakout candidates.
```

The agent creates an intent record under:

```text
docs/pie/<intent>/intent.md
```

The intent is allowed to evolve. Clarification answers, spike findings, and delivery feedback can all update it.

When a new intent is created, PIE checks whether it aligns with the Project Goal. If the intent appears to drift from what the project is fundamentally for, the agent should ask whether to reframe the intent, update the Project Goal, or treat the work as a separate project.

### Spike

A spike is a focused investigation that gathers evidence for one uncertainty in a parent intent.

Example:

```text
Intent: stock-screener
  Spike: vcp-scoring
```

The spike might answer:

```text
How should VCP-like setup quality be scored for ranked output?
```

Spikes are useful when a decision needs data points: a code inspection, a prototype, a benchmark, a UX comparison, an integration check, or a domain experiment. The result is not "research for its own sake"; it is evidence that lets the agent record or recommend decisions.

The durable spike record lives under `docs/pie/`. Spike-only code does not. Put experimental code under:

```text
spikes/<spike>/
```

PIE initialization should enforce this separation in tooling: `spikes/` should be ignored by Git, and `spikes/` plus `docs/pie/` should be excluded from lint, test, build, and package-publish inputs when those tools are present. `/pie:init` and `PIE init` are responsible for adding those excludes before spike code is created.

### Decision

A decision is something that has become stable enough to guide delivery.

PIE records decisions as they settle, including the rationale, source, and impact. If the user answers a material clarifying question, the agent should update the active intent immediately. If a spike provides enough evidence, distillation should record settled decisions or recommend unresolved ones. The user should not need to preserve decisions manually after ordinary clarification.

### Delivery Baseline

A Delivery Baseline is the handoff from PIE into implementation or a downstream framework. It says:

- what should be built;
- what is in scope;
- what is out of scope;
- what decisions are settled;
- what constraints must be honored;
- how success will be judged.

The baseline is not a speculative plan. It is the current stable intent that delivery is allowed to rely on.

Each handoff is traceable. PIE gives the intent a stable ID, snapshots each delivery-ready baseline as a revision, and records each direct implementation or export as a Delivery Ask. That creates a lightweight chain:

```text
PIE Intent ID
-> Delivery Baseline ID
-> Delivery Ask ID
-> Downstream Target ID
```

This matters when the same intent is delivered more than once. A stock screener may export one baseline to [Spec Kit](https://github.com/github/spec-kit), receive planning feedback, reopen discovery, and later export a revised baseline to the same downstream target. PIE keeps those iterations connected without taking over downstream planning.

### Feedback

Feedback is how PIE keeps intent honest during delivery.

Implementation, downstream planning, production use, or stakeholder review may reveal that the original intent was incomplete or wrong. PIE distinguishes routine implementation details from intent-changing feedback. Routine details stay in delivery. Intent-changing discoveries update the intent, baseline, or downstream seed.

Examples:

- implementation shows the existing data model cannot support the requested behavior;
- a spec workflow exposes an undefined success criterion;
- a spike invalidates an assumption;
- a user review changes scope or priority.

In direct implementation, the agent may apply feedback automatically. In downstream delivery, use explicit feedback so PIE can reconcile the discovery before the spec, plan, or design continues.

## Durable State

PIE keeps durable state in files instead of relying on chat history. PIE records live under `docs/pie/`, while spike working code lives under top-level `spikes/`:

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
    preparation-baseline.md
    exports/
    spikes/
      <spike>/
        spike.md
spikes/
  <spike>/
```

## Quick Start: Claude Code

Install PIE from `github.com/qiangxue/pie`:

```text
/plugin marketplace add qiangxue/pie
/plugin install pie@pie
```

Initialize PIE in a project:

```text
/pie:init
```

For a greenfield project, PIE asks for the Project Goal. For a brownfield project, PIE reconstructs a candidate goal and guardrails from existing repo context and asks you to confirm or revise them.

Create your first intent:

```text
/pie:intent stock-screener "Build a local stock screener that finds high-quality breakout candidates."
```

Common commands:

```text
/pie:project
/pie:spike vcp-scoring
/pie:distill
/pie:implement
/pie:export speckit
/pie:export lid
/pie:feedback "Spec Kit planning exposed a new evaluation ambiguity."
/pie:baseline
/pie:decision "Defer intraday signals until after the first production version."
```

Use `/pie:project` to view or adjust the Project Goal and guardrails. Use `/pie:intent <name> <description>` to create intent, `/pie:intent` to list intents, and `/pie:intent <name>` to switch active intent.

For command details, see [docs/claude-code-command-reference.md](docs/claude-code-command-reference.md).

## Quick Start: Codex

Copy or adapt the Codex [AGENTS.md template](plugins/templates/codex/AGENTS.md) into your project root.

If the project already has an `AGENTS.md`, merge the PIE sections instead of replacing existing rules.

Then use concise prompt commands in Codex:

```text
PIE init
PIE project
PIE intent stock-screener: Build a local stock screener that finds high-quality breakout candidates.
PIE spike vcp-scoring: compare binary VCP detection with ranked setup scoring.
PIE distill
PIE implement
```

Codex can also export downstream seeds:

```text
PIE export speckit
PIE export lid
```

For details, see [docs/codex-guide.md](docs/codex-guide.md).

## Working with Delivery Frameworks

PIE can hand delivery-ready intent to downstream delivery frameworks, such as [Spec Kit](https://github.com/github/spec-kit) or [Linked-Intent Development](https://github.com/jszmajda/lid), through adapters. In Claude Code, use:

- [`speckit`](https://github.com/github/spec-kit): `/pie:export speckit` writes `docs/pie/<intent>/exports/speckit-seed.md`.
- [`lid`](https://github.com/jszmajda/lid): `/pie:export lid` writes `docs/pie/<intent>/exports/lid-seed.md`.

In Codex, use `PIE export speckit` or `PIE export lid`.

Adapters produce downstream seeds. They do not replace downstream workflows.

Each export creates a delivery ask record and adds a `PIE Origin` block to the seed, so later feedback can point back to the exact intent and baseline revision that produced the downstream ask.

## Examples

Start with the examples if you want to see PIE in context:

- [Greenfield example](docs/greenfield-example.md): use PIE to shape a new stock screener before implementation.
- [Brownfield example](docs/brownfield-example.md): use PIE to assess and prepare an existing system before adding new behavior.

## Reference

- [Framework specification](docs/framework-specification.md)
- [Claude Code command reference](docs/claude-code-command-reference.md)
- [Codex usage guide](docs/codex-guide.md)
- [Claude Code plugin](plugins/claude-code/pie)
- [Contributing](CONTRIBUTING.md)

## Repository Map

```text
docs/
  framework-specification.md   # definitive framework specification
  claude-code-command-reference.md # Claude Code command model
  codex-guide.md               # Codex usage guide
  greenfield-example.md
  brownfield-example.md
plugins/claude-code/pie/       # Claude Code plugin
  commands/                    # slash-command prompts
  adapters/                    # export adapter specs
  skills/                      # global PIE behavior skill
plugins/templates/
  claude-code/                 # Claude Code AGENTS.md and CLAUDE.md templates
  codex/                       # Codex AGENTS.md template
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Contributions should preserve PIE's main constraints:

- keep the workflow lightweight;
- avoid turning discovery into ceremony;
- maintain durable state outside chat history;
- preserve traceability across baseline revisions, delivery asks, and feedback;
- keep downstream delivery frameworks downstream.

## License

MIT. See [LICENSE](LICENSE).
