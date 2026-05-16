---
description: Reconcile implementation-stage or delivery-stage feedback back into PIE.
---

# PIE Feedback

Use this when implementation or downstream delivery exposes feedback that may affect intent.

For direct implementation mode, the agent may apply this behavior automatically when it discovers intent-changing feedback. When delivery is happening through a downstream framework such as [Spec Kit](https://github.com/github/spec-kit) or [LID](https://github.com/jszmajda/lid), use this command explicitly to classify and reconcile the feedback.

## Process

1. Load the active intent from `docs/pie/index.md`.
2. Identify the feedback source:
   - implementation finding;
   - experiment result;
   - production incident;
   - user or stakeholder feedback;
   - downstream planning issue.
3. Classify the feedback:
   - routine implementation detail;
   - assumption challenged;
   - new material ambiguity;
   - new empirical uncertainty;
   - preparation gap discovered.
4. If it does not change intent, summarize briefly and do not churn PIE artifacts.
5. If an assumption is challenged, update the intent and/or baseline.
6. If new material ambiguity appears, mark the intent `reopened`.
7. If new empirical uncertainty appears, recommend or create a spike.
8. If a downstream framework is in use, identify whether its seed, spec, plan, or design must be regenerated or patched after the PIE update. PIE adapter seeds live under `docs/pie/<intent>/exports/`.
9. Update `docs/pie/index.md`.
10. If urgency forced code ahead of documentation, add an emergency exception note with required follow-up.

## Output

Return:

- `Intent Changed?` yes or no.
- `Feedback Classification`.
- `Artifact Updates`, if any.
- `Delivery Impact`: continue, revise baseline, regenerate downstream seed/spec, reopen Discover, or create preparation work.
- `Recommended Next Step`.
