# Greenfield PIE Example: Stock Screener in Claude Code

This example shows how a first-time PIE user can apply Progressive Intent Engineering to a greenfield stock screener project in Claude Code.

The goal is not to write code immediately. The goal is to mature an unclear intent until it is ready for direct implementation or export to a downstream workflow such as [Spec Kit](https://github.com/github/spec-kit) or [Linked-Intent Development](https://github.com/jszmajda/lid) (LID).

## Scenario

The user starts with:

> I want to build a stock screener based on VCP.

That is a useful starting point, but it is not delivery-ready. "VCP", "stock screener", "candidate quality", data source, output shape, scoring, and evaluation method are all material intent questions.

PIE helps Claude Code avoid building a random technical-analysis tool before those decisions are stable.

## 1. Initialize PIE

Install the PIE plugin from Claude Code:

```text
/plugin marketplace add qiangxue/pie
/plugin install pie@pie
```

Restart Claude Code after installation.

Then initialize PIE in the repository:

```text
/pie:init
```

Claude should create or update:

```text
AGENTS.md
CLAUDE.md
docs/pie/
docs/pie/project.md
docs/pie/index.md
```

Because this is a greenfield project, Claude should ask for the high-level Project Goal:

```text
What is the high-level goal of this project?
```

The user answers:

```text
Build an effective research platform for identifying high-quality stock opportunities before major price moves.
```

Claude may ask a few material project-level clarifying questions, then creates `docs/pie/project.md`:

```md
# PIE Project Context

## Project Goal
Build an effective research platform for identifying high-quality stock opportunities before major price moves.

## Project Guardrails
- Favor empirically testable approaches.
- Keep research findings separate from delivery-ready artifacts.
- Ensure future intents remain aligned with the overall research-platform mission.

## Shared Project Principles
- Prefer strict out-of-sample evaluation where applicable.
- Preserve interpretability when it materially affects decision-making.

## Active Intents
- None yet.
```

Claude should also enforce spike isolation before any spike code is created:

```text
.gitignore       # add spikes/
.eslintignore    # add spikes/ and docs/pie/ when ESLint ignore files are used
.npmignore       # add spikes/ and docs/pie/ for npm packages
```

For other detected tooling, such as Biome, Prettier, Stylelint, test runners, or package publishing, Claude should add equivalent excludes. Existing ignore rules should be preserved.

`docs/pie/index.md` is the durable registry of PIE state. It tracks active intent, active spike, intent status, baseline revisions, delivery asks, downstream targets, child spikes, blockers, and artifact links.

A new index starts roughly like this:

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

## 2. Start a New Intent

Create a named intent:

```text
/pie:intent stock-screener "Create a stock screener based on VCP."
```

Claude should:

- create `docs/pie/stock-screener/intent.md`;
- register `stock-screener` in `docs/pie/index.md`;
- set `stock-screener` as the active intent;
- assess alignment with the Project Goal;
- assess whether the intent is ready, needs clarification, or needs one or more spikes.

The intent artifact should include lightweight metadata:

```md
---
type: intent
intent_id: PIE-INTENT-STOCK-SCREENER
name: stock-screener
project_goal_alignment: aligned
status: discovering
created_at: 2026-05-16
updated_at: 2026-05-16
active: true
delivery_baseline: absent
recommended_next_step: clarify
---

# Intent Brief - Stock Screener
```

Claude may respond:

```text
Intent created: stock-screener
Initial status: discovering

Project alignment:
- Aligned. A VCP-centered screener supports the broader research-platform goal by helping identify high-quality stock opportunities before major moves.

Current understanding:
- Build a stock screener centered on VCP-like setups.

Material unknowns:
1. Should the screener detect textbook VCP patterns only, or broader actionable pre-impulse setups?
2. What should the screener optimize for: visual setup quality, future breakout performance, risk/reward quality, or a combination?
3. Should the output be binary matches, ranked candidates, or staged setup states?

Recommended next step:
- Clarify questions 1-3.
- After that, consider a spike on scoring and evaluation design.
```

## 3. Clarify and Auto-Record Settled Decisions

The user answers the initial clarification questions:

> I care more about finding actionable candidates than identifying textbook VCPs. Ranked output is better than binary output. Use end-of-day data for the first version.

Claude should automatically apply the Convergence Rule. The user does not need to run `/pie:distill` for this ordinary clarification-answer flow.

Claude immediately updates `docs/pie/stock-screener/intent.md` because the answer resolves tracked ambiguities and creates settled decisions:

```md
## Decisions Made

### Decision: Prioritize actionable candidates
- **Decision:** The screener should prioritize actionable pre-impulse candidates rather than strict textbook VCP purity.
- **Rationale:** The user explicitly said actionable candidates matter more than textbook pattern identification.
- **Source:** User clarification.
- **Impact:** VCP structure becomes important evidence, but not the sole product definition.
- **Status:** Accepted.

### Decision: Use ranked output
- **Decision:** The screener output should be ranked rather than binary pass/fail.
- **Rationale:** The user explicitly preferred ranked output.
- **Source:** User clarification.
- **Impact:** Candidate scoring becomes central to the Delivery Baseline.
- **Status:** Accepted.

### Decision: Use daily data for v1
- **Decision:** The first version should use daily OHLCV data only.
- **Rationale:** This keeps v1 tractable and matches the initial use case.
- **Source:** User clarification.
- **Impact:** Intraday signals are deferred.
- **Status:** Accepted.
```

Claude also updates current understanding:

```md
## Current Understanding
- The project is a pre-impulse candidate screener centered on VCP-style structure, not a strict textbook VCP classifier.
- The first version should use ranked output and daily OHLCV data.
```

And it removes resolved unknowns:

```md
## Material Unknowns
- Removed: Whether the project should target textbook VCPs only or broader actionable setups.
- Removed: Whether output should be binary or ranked.
- Removed: Whether v1 should use daily or intraday data.
```

The intent status may move from `discovering` to `needs_spike` because the product direction is clearer but empirical questions remain.

`/pie:decision` is not needed here because the decisions were already settled by the clarification response. `/pie:distill` is also not needed because this was a local convergence update, not a broad synthesis.

## 4. List or Switch Intents

If multiple intents are active, use:

```text
/pie:intent
```

Claude lists durable state from `docs/pie/index.md`:

```text
Intent | Status | Delivery Baseline | Open Spikes
stock-screener | discovering | absent | 0
alerting-dashboard | ready_for_delivery | absent | 0

Active intent:
- stock-screener
```

To switch context:

```text
/pie:intent stock-screener
```

Claude sets `stock-screener` as active in the index and summarizes its current durable state.

## 5. Create a Spike

Suppose Claude identifies a material empirical uncertainty:

> How should VCP-related setup quality be represented for ranked output?

Create a spike under the active intent:

```text
/pie:spike vcp-scoring
```

Claude creates:

```text
docs/pie/stock-screener/spikes/vcp-scoring/spike.md
```

The spike artifact should include metadata:

```md
---
type: spike
name: vcp-scoring
parent_intent: stock-screener
status: active
created_at: 2026-05-16
updated_at: 2026-05-16
decision_target: "How should VCP candidates be scored and ranked?"
distilled_into_parent: false
---
```

The spike defines:

```md
# Spike - VCP Scoring

## Question
How should setup quality be represented for ranked output?

## Hypothesis
A staged score combining compression, demand quality, pivot proximity, and structural damage is more useful than a binary pattern classifier.

## Method
Compare candidate scoring formulations against a small set of historical examples.

## Evaluation
Review whether top-ranked results better represent actionable setups than binary textbook-VCP detection.

## Decision Impact
Determines the likely core scoring architecture of the screener.
```

The agent then works interactively with the user to implement and analyze the spike. Before writing `spikes/vcp-scoring/scoring-prototype.ts`, Claude should verify that `/pie:init` has excluded `spikes/` from Git tracking and excluded `spikes/` and `docs/pie/` from applicable lint, test, build, and package-publish tooling. If those excludes are missing, Claude should add them before creating or running spike code.

## 6. Distill the Spike

After the scoring spike produces findings, run:

```text
/pie:distill
```

Because `vcp-scoring` is the active spike, Claude should:

- summarize what the spike learned;
- update `docs/pie/stock-screener/spikes/vcp-scoring/spike.md`;
- update the parent intent with material findings;
- record decisions that are already settled by accepted spike findings;
- recommend decisions that still require human approval;
- mark the spike `distilled` if its findings have been incorporated.

Example response:

```text
Spike distilled: vcp-scoring

Key findings:
- A binary textbook-VCP classifier is too narrow for the intended use.
- A staged scoring model better supports ranked candidate selection.
- Compression, contraction structure, demand quality, and pivot proximity should be separate evidence blocks.

Recommended intent decision:
- Adopt a staged scoring architecture rather than a pure pass/fail detector.

Recorded in parent intent:
- Added decision: Use ranked staged scoring instead of binary textbook-VCP classification.
- Updated Current Understanding.
- Removed the ambiguity around binary versus ranked output.

Status changes:
- vcp-scoring: distilled
- stock-screener: needs_spike
```

No separate `/pie:decision` is required if the spike result and prior user agreement clearly settled the question.

If a spike suggests a broader judgment call that the user has not approved, Claude should recommend but not record it as accepted:

```text
Recommended decision:
- Reframe the product from a strict VCP screener into a broader pre-impulse setup screener.

This would materially broaden the project framing, so it has not been recorded as accepted.
```

The user can accept or override that recommendation explicitly:

```text
/pie:decision "Treat VCP as an evidence block inside a broader pre-impulse setup screener."
```

If further empirical uncertainty remains, such as evaluation design, create another spike:

```text
/pie:spike evaluation-method
```

After that spike is analyzed, run:

```text
/pie:distill
```

Use `/pie:distill` the same way after a long or branched discovery conversation, even if no spike is active. It is for holistic consolidation: several partial conclusions, trade-offs, evidence notes, readiness changes, or a deliberate checkpoint before switching sessions. It is not required after a single clear clarification answer.

## 7. Move Toward Delivery

Suppose the active intent now has:

- ranked output chosen;
- end-of-day data chosen;
- staged scoring chosen;
- evaluation method chosen;
- CLI output chosen for the first version;
- no active undistilled spikes.

Claude should mark the intent `ready_for_delivery` in `docs/pie/stock-screener/intent.md` and `docs/pie/index.md`.

You do not have to run `/pie:baseline` in the normal flow. Baseline generation happens automatically when delivery starts.

## 8A. Export to [Spec Kit](https://github.com/github/spec-kit)

If the user wants a [Spec Kit](https://github.com/github/spec-kit) workflow:

```text
/pie:export speckit
```

Claude should:

1. load the active intent from `docs/pie/index.md`;
2. run the Ready for Delivery check;
3. create or refresh `docs/pie/stock-screener/baseline.md`;
4. create an immutable baseline snapshot, for example `docs/pie/stock-screener/baselines/PIE-BASELINE-STOCK-SCREENER-R1.md`;
5. create a delivery ask record, for example `docs/pie/stock-screener/asks/PIE-ASK-STOCK-SCREENER-SPECKIT-001.md`;
6. export a [Spec Kit](https://github.com/github/spec-kit) seed, for example `docs/pie/stock-screener/exports/speckit-seed.md`;
7. include PIE origin metadata in the seed;
8. mark the intent `in_delivery`.

Example response:

```text
Intent stock-screener is ready for delivery.

Actions taken:
1. Generated Delivery Baseline.
2. Created baseline revision PIE-BASELINE-STOCK-SCREENER-R1.
3. Created delivery ask PIE-ASK-STOCK-SCREENER-SPECKIT-001.
4. Exported [Spec Kit](https://github.com/github/spec-kit) seed from the baseline revision.
5. Marked the intent as in_delivery.

Export target:
- [Spec Kit](https://github.com/github/spec-kit)

PIE origin:
- PIE Intent: PIE-INTENT-STOCK-SCREENER
- PIE Delivery Baseline: PIE-BASELINE-STOCK-SCREENER-R1
- PIE Delivery Ask: PIE-ASK-STOCK-SCREENER-SPECKIT-001
```

PIE remains upstream. [Spec Kit](https://github.com/github/spec-kit) owns the structured specification, plan, tasks, and implementation workflow.

## 8B. Export to [LID](https://github.com/jszmajda/lid)

If the user wants a [Linked-Intent Development](https://github.com/jszmajda/lid) workflow:

```text
/pie:export lid
```

Claude should:

1. load the active intent from `docs/pie/index.md`;
2. run the Ready for Delivery check;
3. create or refresh `docs/pie/stock-screener/baseline.md`;
4. create an immutable baseline snapshot;
5. create a delivery ask record;
6. export a [LID](https://github.com/jszmajda/lid) seed, for example `docs/pie/stock-screener/exports/lid-seed.md`;
7. include PIE origin metadata in the seed;
8. mark the intent `in_delivery`.

The [LID](https://github.com/jszmajda/lid) seed should translate the Delivery Baseline into HLD/LLD/EARS-oriented starting material:

```text
PIE Goal -> HLD purpose
PIE Context -> HLD background
PIE In-Scope Intent -> candidate LLD segment scope
PIE Clarified Decisions -> HLD/LLD design decisions
PIE Success Criteria -> candidate EARS claims and validation targets
```

PIE remains upstream. [LID](https://github.com/jszmajda/lid) owns the HLD, LLDs, EARS specs, failing-first tests, code, and `@spec` traceability.

## 8C. Implement Directly

If the user wants direct coding instead:

```text
/pie:implement
```

Claude should:

1. load the active intent from `docs/pie/index.md`;
2. run the Ready for Delivery check;
3. create or refresh `docs/pie/stock-screener/baseline.md`;
4. create an immutable baseline snapshot;
5. create a delivery ask record, for example `docs/pie/stock-screener/asks/PIE-ASK-STOCK-SCREENER-DIRECT-001.md`;
6. mark the intent `in_delivery`;
7. begin implementation from the baseline revision.

A likely first project shape:

```text
package.json
src/
  screener/
    data.ts
    scoring.ts
    cli.ts
    output.ts
tests/
  scoring.test.ts
docs/
  pie/
    index.md
    stock-screener/
      intent.md
      baseline.md
      baselines/
        PIE-BASELINE-STOCK-SCREENER-R1.md
      asks/
        PIE-ASK-STOCK-SCREENER-SPECKIT-001.md
        PIE-ASK-STOCK-SCREENER-DIRECT-001.md
      exports/
        speckit-seed.md
        lid-seed.md
      spikes/
        vcp-scoring/
          spike.md
        evaluation-method/
          spike.md
spikes/
  vcp-scoring/
    scoring-notes.md
    scoring-prototype.ts
```

PIE is no longer the main activity once delivery begins. It remains the intent source of truth.

## 9. Feedback Loop

Delivery is allowed to teach PIE when it changes the intended work.

During direct implementation, Claude may automatically apply feedback behavior if it discovers an intent-changing issue. For example:

> The ranking logic cannot be judged meaningfully without defining whether future breakout performance or visual setup quality is the primary evaluation target.

Claude should classify the finding, reopen the intent if needed, update the baseline or intent, and recommend a new spike.

When implementation is happening through a downstream delivery framework such as [Spec Kit](https://github.com/github/spec-kit), use feedback explicitly:

```text
/pie:feedback "Spec Kit planning showed that ranking quality cannot be specified without choosing whether evaluation targets future breakout performance, visual setup quality, or risk/reward quality."
```

If the user knows the exact ask, include it in the feedback:

```text
/pie:feedback "PIE-ASK-STOCK-SCREENER-SPECKIT-001 showed that ranking quality cannot be specified without choosing whether evaluation targets future breakout performance, visual setup quality, or risk/reward quality."
```

Claude should respond:

```text
Feedback classification:
- Intent-relevant
- New material ambiguity discovered

Impact:
- The Delivery Baseline assumes the screening objective is sufficiently stable.
- The [Spec Kit](https://github.com/github/spec-kit) seed should not proceed until the evaluation target is clarified.

Recommended action:
1. Reopen stock-screener.
2. Add a new material unknown about ranking-quality evaluation.
3. Recommend spike: evaluation-method.
4. Mark the current Delivery Baseline as needing revision.
```

This closes the PIE loop:

```text
delivery finding
-> PIE feedback
-> update intent or baseline
-> create spike if needed
-> distill
-> regenerate baseline/export when ready
```

## 10. Final Workflow

The clean greenfield command workflow is:

```text
/pie:init
/pie:project
/pie:intent <name> <description>
/pie:intent <name>
/pie:intent
/pie:spike <name>
/pie:spike
/pie:distill
/pie:implement
/pie:export <adapter>
/pie:feedback <description>
```

With optional commands:

```text
/pie:baseline
/pie:decision <description>
```

`/pie:baseline` previews or explicitly generates a baseline without delivery. `/pie:decision` manually records, affirms, or overrides a decision; it is not required after every `/pie:distill`.
