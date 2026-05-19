# Greenfield Example: Stock Screener

This example shows how a first-time PIE user can shape a new stock screener before implementation.

The user starts with:

> I want to build a stock screener based on VCP.

That is useful, but not delivery-ready. "VCP", "candidate quality", data source, output shape, scoring, and evaluation all affect what should be built.

PIE helps the agent avoid coding a plausible but misaligned technical-analysis tool before the intent is stable.

## 1. Initialize PIE

Install the Claude Code plugin:

```text
/plugin marketplace add qiangxue/pie
/plugin install pie@pie
```

Initialize PIE in the repository:

```text
/pie:init
```

Because this is greenfield, Claude asks for the Project Goal:

```text
What is the high-level goal of this project?
```

User:

```text
Build an effective research platform for identifying high-quality stock opportunities before major price moves.
```

Claude creates:

```text
AGENTS.md
CLAUDE.md
docs/pie/project.md
docs/pie/index.md
```

`docs/pie/project.md` captures the durable project context:

```md
# PIE Project Context

## Project Goal
Build an effective research platform for identifying high-quality stock opportunities before major price moves.

## Project Guardrails
- Favor empirically testable approaches.
- Keep research findings separate from delivery-ready artifacts.
- Keep future intents aligned with the research-platform mission.

## Shared Project Principles
- Prefer strict out-of-sample evaluation when applicable.
- Preserve interpretability when it materially affects decisions.
```

`/pie:init` also enforces spike isolation by excluding `spikes/` from Git and excluding both `spikes/` and `docs/pie/` from lint, test, build, and package-publish inputs when those tools are present. `docs/pie/` remains versioned durable state.

## 2. Create The First Intent

```text
/pie:intent stock-screener "Create a stock screener based on VCP."
```

Claude should:

- assess alignment with the Project Goal;
- create `docs/pie/stock-screener/intent.md`;
- register the intent in the derived `docs/pie/index.md`;
- select it for the current session;
- assess readiness.

Example response:

```text
Intent created: stock-screener
Project alignment: aligned

Current understanding:
- Build a screener centered on VCP-like setups.

Material unknowns:
1. Should the screener detect textbook VCPs only, or broader actionable pre-impulse setups?
2. Should output be binary matches, ranked candidates, or staged setup states?
3. What data resolution is in scope for v1?

Recommended next step:
- Clarify questions 1-3.
- Then run a spike on scoring and evaluation design.
```

The intent carries a stable ID:

```yaml
intent_id: PIE-INTENT-STOCK-SCREENER
project_goal_alignment: aligned
status: discovering
```

## 3. Clarify And Converge

User:

> I care more about finding actionable candidates than identifying textbook VCPs. Ranked output is better than binary output. Use end-of-day data for the first version.

Claude should automatically record the explicit decisions: ranked output and daily data for v1. The broader statement about actionable candidates implies a framing shift, so Claude should confirm before recording it as accepted:

```text
This suggests a material intent decision:
- Treat VCP as an evidence block inside a broader actionable pre-impulse screener.

Should I record that as the current intent direction?
```

User:

```text
Yes, record that.
```

No `/pie:distill` is needed for this local convergence.

The intent records decisions such as:

```md
### Decision: Prioritize actionable candidates
- **Decision ID:** DEC-STOCK-SCREENER-001
- **Decision:** The screener should prioritize actionable pre-impulse candidates rather than strict textbook VCP purity.
- **Rationale:** The user values candidate discovery over textbook pattern identification.
- **Source:** clarification_response
- **Impact:** VCP structure becomes important evidence, but not the sole product definition.
- **Status:** accepted

### Decision: Use ranked output
- **Decision ID:** DEC-STOCK-SCREENER-002
- **Decision:** The screener output should be ranked rather than binary pass/fail.
- **Rationale:** The user explicitly preferred ranked candidates.
- **Source:** clarification_response
- **Impact:** Candidate scoring becomes central.
- **Status:** accepted

### Decision: Use daily data for v1
- **Decision ID:** DEC-STOCK-SCREENER-003
- **Decision:** Version 1 uses daily OHLCV data only.
- **Rationale:** This keeps the first version tractable.
- **Source:** clarification_response
- **Impact:** Intraday signals are deferred.
- **Status:** accepted
```

The status may move to `needs_spike` because the product direction is clearer, but scoring still needs evidence.

## 4. Create A Spike

```text
/pie:spike vcp-scoring
```

Claude creates:

```text
docs/pie/stock-screener/spikes/vcp-scoring/spike.md
spikes/vcp-scoring/
```

The spike focuses on one uncertainty:

```md
# Spike - VCP Scoring

## Question
How should setup quality be represented for ranked output?

## Hypothesis
A staged score using compression, demand quality, pivot proximity, and structural damage is more useful than a binary classifier.

## Decision Impact
Determines the likely core scoring architecture.
```

If Claude writes exploratory code, it goes under `spikes/vcp-scoring/`, not under `docs/`. Spike code is exploratory by default; downstream delivery may reuse, rewrite, or discard it according to ordinary engineering judgment.

## 5. Distill Findings

After the spike:

```text
/pie:distill
```

Claude should:

- summarize findings;
- update the spike record;
- update the parent intent;
- record decisions already settled by evidence and user agreement;
- recommend decisions that still need approval;
- update readiness.

Example:

```text
Spike distilled: vcp-scoring

Key findings:
- Binary textbook-VCP detection is too narrow.
- Ranked staged scoring better supports actionable candidate selection.
- Compression, demand quality, pivot proximity, and structural damage should remain separate evidence blocks.

Recorded decision:
- Use staged ranked scoring instead of binary textbook-VCP classification.

Status:
- stock-screener: needs_spike -> ready_for_delivery
```

## 6. Deliver

When readiness returns `ready`, choose a delivery path. If readiness is `borderline`, Claude should name the judgment call and ask before delivery.

Direct implementation:

```text
/pie:implement
```

Export to [Spec Kit](https://github.com/github/spec-kit):

```text
/pie:export speckit
```

Export to [LID](https://github.com/jszmajda/lid):

```text
/pie:export lid
```

Each delivery command creates a baseline revision and a delivery ask:

```text
docs/pie/stock-screener/baseline.md
docs/pie/stock-screener/baselines/PIE-BASELINE-STOCK-SCREENER-R1.md
docs/pie/stock-screener/asks/PIE-ASK-STOCK-SCREENER-SPECKIT-001.md
docs/pie/stock-screener/exports/speckit-seed.md
```

The downstream seed includes:

```md
## PIE Origin
- PIE Intent: `PIE-INTENT-STOCK-SCREENER`
- PIE Delivery Baseline: `PIE-BASELINE-STOCK-SCREENER-R1`
- PIE Delivery Ask: `PIE-ASK-STOCK-SCREENER-SPECKIT-001`
```

## 7. Feed Back Delivery Learning

If delivery reveals that the intent is incomplete, use feedback.

```text
/pie:feedback "PIE-ASK-STOCK-SCREENER-SPECKIT-001 showed that ranking quality cannot be specified without choosing whether evaluation targets future breakout performance, visual setup quality, or risk/reward quality."
```

Claude should:

- classify the feedback as intent-impacting;
- reopen the intent if needed;
- record the challenged assumption;
- recommend clarification or a new spike;
- mark the current baseline as needing revision;
- regenerate or patch downstream artifacts after PIE is updated.

Routine implementation details should not churn PIE artifacts.

## Command Recap

```text
/pie:init
/pie:project
/pie:intent <name> <description>
/pie:spike <name>
/pie:distill
/pie:implement
/pie:export <adapter>
/pie:feedback <description>
```

Optional:

```text
/pie:baseline
/pie:decision <description>
```
