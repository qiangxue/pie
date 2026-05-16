# Using PIE With Codex

PIE works with Codex through project guidance and repeatable prompt patterns.

Use PIE in Codex through:

- `AGENTS.md` project guidance;
- concise PIE prompt commands;
- durable files under `docs/pie/`;
- Codex built-in commands such as `/plan`, `/compact`, `/status`, `/review`, and `/plugins` when useful.

Official Codex references:

- [Custom instructions with AGENTS.md](https://developers.openai.com/codex/guides/agents-md)
- [Slash commands in Codex CLI](https://developers.openai.com/codex/cli/slash-commands)

## 1. Add PIE Guidance

Copy or adapt:

```text
plugins/templates/codex/AGENTS.md
```

into the target project's root `AGENTS.md`.

If the project already has an `AGENTS.md`, merge the PIE sections into it instead of replacing existing repository rules.

## 2. Initialize PIE With a Prompt

In Codex, ask:

```text
Use PIE. Initialize durable PIE state in this repository:
- create docs/pie/index.md if missing;
- preserve existing AGENTS.md guidance;
- summarize the active PIE command model;
- do not start implementation yet.
```

## 3. Use PIE Prompt Commands

Use short, repeatable prompts. For example:

| Prompt | Behavior |
|---|---|
| `PIE init` | Initialize durable PIE state in this repo. |
| `PIE intent stock-screener: ...` | Create or update an intent. |
| `PIE list intents` | List intents and active context. |
| `PIE switch intent stock-screener` | Switch active intent. |
| `PIE spike vcp-scoring: ...` | Create or continue a spike. |
| `PIE distill` | Synthesize the active spike or recent discovery into durable artifacts. |
| `PIE decision: ...` | Manually record, affirm, reject, or override a decision. |
| `PIE implement` | Readiness-check, baseline, then implement. |
| `PIE export speckit` | Readiness-check, baseline, then write the [Spec Kit](https://github.com/github/spec-kit) seed. |
| `PIE export lid` | Readiness-check, baseline, then write the [LID](https://github.com/jszmajda/lid) seed. |
| `PIE feedback: ...` | Reconcile delivery feedback back into PIE. |
| `PIE baseline` | Preview or refresh the Delivery Baseline without starting delivery. |

The important part is not the prefix. The important part is that Codex follows the durable-state behavior in `AGENTS.md` and updates `docs/pie/`.

## 4. Use Codex Built-In Slash Commands Around PIE

Codex slash commands are useful around PIE, but they are not the PIE workflow itself.

Examples:

```text
/plan PIE intent stock-screener: Build a local stock screener that ranks high-quality breakout candidates.
```

Use `/plan` when you want Codex to discuss the approach before editing files.

```text
/compact
```

Use `/compact` after a long discovery session. Then ask `PIE distill` to refresh durable artifacts if important conclusions accumulated.

```text
/status
```

Use `/status` to inspect the Codex session. Use `PIE list intents and show active context` to inspect PIE state.

## 5. Spike Code Location

Spike records live under:

```text
docs/pie/<intent>/spikes/<spike>/
```

The spike record is:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
```

Spike-only code, fixtures, scripts, notes, and prototypes should live outside `docs/` under:

```text
spikes/<spike>/
```

If a spike must touch real project files, Codex should record the touched paths and the reason in `spike.md`. Spike code should not become production code unless it is explicitly promoted after distillation or delivery planning.

## 6. Practical Codex Session

```text
PIE init
```

```text
PIE intent stock-screener: Build a local stock screener that ranks high-quality pre-breakout candidates.
```

Codex may ask a material clarifying question. When you answer, Codex should apply the Convergence Rule and update the intent automatically.

If the answer needs evidence:

```text
PIE spike vcp-scoring: compare binary VCP detection with ranked setup scoring.
```

After the spike work:

```text
PIE distill: synthesize the active spike into the parent intent and record settled decisions.
```

When the intent is ready:

```text
PIE implement: readiness-check, create or refresh the Delivery Baseline, and begin direct implementation.
```
