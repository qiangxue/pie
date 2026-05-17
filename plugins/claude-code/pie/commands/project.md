---
description: Display or update PIE project-level goal, guardrails, and shared principles.
---

# PIE Project

Display the current project-level PIE context.

The Project Goal is not an intent and does not enter the discovery -> baseline -> delivery lifecycle. It is the project-level guardrail that individual intents must align with.

## Process

1. Load `docs/pie/project.md`.
2. If it is missing, recommend `/pie:init` or create it using the same greenfield/brownfield behavior as `/pie:init`.
3. Display the current:
   - Project Goal;
   - Project Guardrails;
   - Shared Project Principles;
   - Current System Understanding, for brownfield projects;
   - Known Evolution Themes, when present;
   - Open Project-Level Questions, when present;
   - Active Intents.
4. End with a light invitation to update the Project Goal, guardrails, or shared principles.

Do not make project updates mandatory. The command is primarily for inspection and lightweight adjustment.

## Update Behavior

If the user asks to update project context:

1. Update `docs/pie/project.md`.
2. Update `docs/pie/index.md` if active intent alignment, project artifact links, or project-level questions changed.
3. If the update affects existing intents, list which intents may need alignment review.

If repeated intents suggest the project mission has changed, explicitly surface that as project drift and ask whether to update the Project Goal or treat the work as a separate project.

## Output

Return:

- `Project Goal`.
- `Project Guardrails`.
- `Shared Principles`.
- `Brownfield Context`, when present.
- `Open Project-Level Questions`, when present.
- `Active Intents`.
- `Update Invitation`: a brief question asking whether the user wants to update project context.
