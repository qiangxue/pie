# Using PIE With Codex

Codex can follow PIE through project guidance and repeatable prompt patterns.

Use PIE in Codex with:

- a project `AGENTS.md`;
- durable files under `docs/pie/`;
- prompt commands such as `PIE intent ...`;
- Codex built-ins such as `/plan`, `/compact`, `/status`, and `/review` when useful.

Official Codex references:

- [Custom instructions with AGENTS.md](https://developers.openai.com/codex/guides/agents-md)
- [Slash commands in Codex CLI](https://developers.openai.com/codex/cli/slash-commands)

## 1. Add PIE Guidance

Copy or merge:

```text
plugins/templates/codex/AGENTS.md
```

into the target project's root `AGENTS.md`.

If the project already has an `AGENTS.md`, merge the PIE sections instead of replacing existing repository rules.

## 2. Initialize PIE

Ask Codex:

```text
PIE init
```

Codex should:

- create or confirm `docs/pie/project.md`;
- create `docs/pie/index.md` if missing;
- enforce spike isolation in Git, lint, test, build, and package-publish tooling;
- preserve existing project guidance.

For greenfield projects, Codex asks for the Project Goal. For brownfield projects, Codex inspects durable repo context, proposes a reconstructed goal and guardrails, and asks for confirmation or revision.

## 3. Prompt Commands

These are prompt patterns, not shell commands:

| Prompt | Behavior |
|---|---|
| `PIE init` | Initialize Project Goal and durable PIE state. |
| `PIE project` | Show or update Project Goal and guardrails. |
| `PIE intent <name>: <description>` | Create or update an intent under the Project Goal. |
| `PIE list intents` | List intents and active context. |
| `PIE switch intent <name>` | Switch active intent. |
| `PIE spike <name>: <question>` | Create or continue a spike. |
| `PIE distill` | Fold spike findings or long discovery into durable artifacts. |
| `PIE decision: <description>` | Manually record or override a decision. |
| `PIE implement` | Run the readiness gate, create baseline revision and ask, then implement. |
| `PIE export speckit` | Run the readiness gate, create baseline revision and ask, then write the [Spec Kit](https://github.com/github/spec-kit) seed. |
| `PIE export lid` | Run the readiness gate, create baseline revision and ask, then write the [LID](https://github.com/jszmajda/lid) seed. |
| `PIE feedback: <description>` | Reconcile delivery feedback back into PIE. |
| `PIE baseline` | Optional baseline preview without delivery. |

The wording can vary. What matters is that Codex updates durable PIE artifacts and does not rely on chat history as the source of truth.

## 4. Normal Session

```text
PIE init
```

```text
PIE intent stock-screener: Build a local stock screener that ranks high-quality pre-breakout candidates.
```

Codex should assess the intent against `docs/pie/project.md`, ask only material clarification questions, and update the intent when answers settle decisions.

If evidence is needed:

```text
PIE spike vcp-scoring: compare binary VCP detection with ranked setup scoring.
```

After the spike:

```text
PIE distill
```

When the readiness gate passes:

```text
PIE implement
```

or:

```text
PIE export speckit
PIE export lid
```

## 5. Spike Code

Spike records live under:

```text
docs/pie/<intent>/spikes/<spike>/spike.md
```

Spike-only code lives under:

```text
spikes/<spike>/
```

Before creating or running spike code, Codex should verify the isolation excludes created by `PIE init`.

## 6. Codex Built-Ins Around PIE

Use `/plan` when you want Codex to discuss the PIE action before editing files:

```text
/plan PIE intent stock-screener: Build a local stock screener that ranks high-quality breakout candidates.
```

Use `/compact` after a long discovery session, then run `PIE distill` if important conclusions accumulated.

Use `/status` for the Codex session and `PIE list intents` for PIE state.

Use `/review` after implementation as usual. If the review exposes intent-changing feedback, run `PIE feedback: ...`.
