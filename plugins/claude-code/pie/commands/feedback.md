---
description: Reconcile implementation-stage or delivery-stage feedback back into PIE.
---

# PIE Feedback

Use this when implementation or downstream delivery exposes feedback that may affect intent.

For direct implementation mode, the agent may apply this behavior automatically when it discovers intent-changing feedback. When delivery is happening through a downstream framework such as [Spec Kit](https://github.com/github/spec-kit) or [LID](https://github.com/jszmajda/lid), use this command explicitly to classify and reconcile the feedback.

## Process

1. Load `docs/pie/index.md`.
2. Identify the active intent or infer the intent from a supplied delivery ask ID.
3. Identify the feedback source lineage:
   - supplied `ask_id`, if present in the feedback;
   - otherwise the active intent's latest ask;
   - associated `baseline_id`;
   - downstream target ID and framework, when known.
4. Identify the feedback source:
   - implementation finding;
   - experiment result;
   - production incident;
   - user or stakeholder feedback;
   - downstream planning issue.
5. Classify the feedback:
   - routine implementation detail;
   - assumption challenged;
   - new material ambiguity;
   - new empirical uncertainty;
   - preparation gap discovered.
6. If it does not change intent, summarize briefly and do not churn PIE artifacts.
7. If an assumption is challenged, update the intent and/or baseline.
8. If new material ambiguity appears, mark the intent `reopened`.
9. If new empirical uncertainty appears, recommend or create a spike.
10. If feedback suggests project-level drift, recommend `/pie:project` and ask whether the Project Goal or guardrails should change.
11. If a downstream framework is in use, identify whether its seed, spec, plan, or design must be regenerated or patched after the PIE update. PIE adapter seeds live under `docs/pie/<intent>/exports/`.
12. Update `docs/pie/index.md` with feedback lineage and delivery impact.
13. If urgency forced code ahead of documentation, add an emergency exception note with required follow-up.

## Feedback Lineage

When known, record feedback source metadata in the active intent:

```yaml
feedback_source:
  ask_id: PIE-ASK-<INTENT-SLUG>-<TARGET>-<NNN>
  baseline_id: PIE-BASELINE-<INTENT-SLUG>-R<N>
  downstream_target_id: <target-id>
  target_framework: direct|speckit|lid
```

This lineage identifies which baseline assumption was challenged and whether downstream artifacts should be regenerated, patched, or left alone.

## Output

Return:

- `Intent Changed?` yes or no.
- `Feedback Classification`.
- `Feedback Source`: ask ID, baseline ID, target framework, and downstream target when known.
- `Artifact Updates`, if any.
- `Delivery Impact`: continue, revise baseline, regenerate downstream seed/spec, reopen Discover, or create preparation work.
- `Recommended Next Step`.
