---
description: Initialize PIE durable state and project guidance in a repository.
---

# PIE Init

Initialize PIE in the repository.

## Process

1. Inspect existing `AGENTS.md`, `CLAUDE.md`, `.claude/`, and `docs/pie/` conventions.
2. Use `${CLAUDE_PLUGIN_ROOT}/../../../templates/claude-code/AGENTS.md` as the AGENTS template when available from the repository checkout.
3. Use `${CLAUDE_PLUGIN_ROOT}/../../../templates/claude-code/CLAUDE.md` as the Claude Code shim template when available from the repository checkout.
4. If those files are not directly readable in the installed plugin environment, synthesize equivalent project guidance from the `pie-core` skill and this command's durable-state rules.
5. If no `AGENTS.md` exists, create one from the template and adapt obvious project-specific details.
6. If `AGENTS.md` exists, add a focused PIE section instead of replacing unrelated repository rules.
7. If no `CLAUDE.md` exists, create a minimal `CLAUDE.md` that imports `@AGENTS.md` and lists the PIE plugin commands.
8. If `CLAUDE.md` exists, preserve it and add `@AGENTS.md` only when doing so will not duplicate or conflict with existing project instructions.
9. Create `docs/pie/` if it does not exist.
10. Create `docs/pie/project.md` if it does not exist.
11. Create `docs/pie/index.md` if it does not exist.
12. If `docs/pie/index.md` exists, preserve current entries and normalize only obviously missing sections.
13. Enforce spike isolation in repository tooling:
   - ensure `.gitignore` contains `spikes/`;
   - ensure `.eslintignore` contains `spikes/` and `docs/pie/` when the repository uses ESLint ignore files;
   - ensure `.npmignore` contains `spikes/` and `docs/pie/` when the repository is an npm package or already has `.npmignore`;
   - apply equivalent excludes for detected tooling such as Biome, Rome, Prettier, Stylelint, test runners, package-publish configs, or language-specific lint/build systems;
   - preserve existing ignore entries and comments; append missing PIE entries under a short `# PIE` section.
14. Preserve existing build, test, style, security, and environment instructions.

## Project Context

`/pie:init` must create durable project-level context before the first intent is created.

For a greenfield repository:

1. Ask: "What is the high-level goal of this project?"
2. Ask only material follow-up questions about project mission, guardrails, or shared principles.
3. Create `docs/pie/project.md`.

For a brownfield repository:

1. Detect or ask whether this is an existing system.
2. Inspect durable context where available:
   - README files;
   - architecture docs;
   - ADRs;
   - product docs;
   - repo structure;
   - existing `AGENTS.md` or `CLAUDE.md` guidance.
3. Propose a reconstructed Project Goal and guardrails.
4. Ask the user to confirm or revise.
5. Create `docs/pie/project.md`.

Do not invent a brownfield Project Goal without confirmation.

## Project Context Template

Use this shape for greenfield projects:

```md
# PIE Project Context

## Project Goal

## Project Guardrails

## Shared Project Principles

## Active Intents
- None yet.
```

Use this shape for brownfield projects:

```md
# PIE Project Context

## Project Goal

## Current System Understanding

## Project Guardrails

## Known Evolution Themes

## Project-Level Decisions

## Open Project-Level Questions

## Active Intents
- None yet.
```

## Index Template

Use this shape for a new `docs/pie/index.md`:

```md
# PIE Index

## Project Context
- Project artifact: docs/pie/project.md

## Active Context
- Active intent: none
- Active spike: none

## Intents
| Intent | Intent ID | Status | Delivery Baseline | Latest Ask | Open Spikes | Last Updated |
|---|---|---|---|---|---:|---|

## Intent Details
```

## Output

Make the smallest useful repository change, then summarize:

- which project-level PIE conventions were added;
- whether `docs/pie/project.md` was created or updated;
- whether `docs/pie/index.md` was created or updated;
- which ignore/tooling files were updated for spike isolation;
- where PIE artifacts should live;
- which slash commands the project should use.
