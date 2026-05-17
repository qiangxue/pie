# PIE Export Adapters

Adapters translate a canonical PIE Delivery Baseline into downstream workflow seed artifacts.

PIE remains upstream. Adapters should not absorb the downstream workflow. They should produce enough structured input for the downstream framework to begin from stable intent without silently inventing product meaning.

## Available Adapters

- [`speckit`](https://github.com/github/spec-kit): creates a [Spec Kit](https://github.com/github/spec-kit) seed for `/speckit.specify` and, where useful, `/speckit.plan`.
- [`lid`](https://github.com/jszmajda/lid): creates a [Linked-Intent Development](https://github.com/jszmajda/lid) seed for HLD/LLD/EARS-oriented design work.

## Output Location

Adapters write under the active intent:

```text
docs/pie/<intent>/exports/
  speckit-seed.md
  lid-seed.md
```

The active intent, baseline state, and export artifact path must also be reflected in `docs/pie/index.md`.

Each export must also create or reference:

```text
docs/pie/<intent>/baselines/<baseline_id>.md
docs/pie/<intent>/asks/<ask_id>.md
```

Adapter output should include:

```md
## PIE Origin
- PIE Intent: `<intent_id>`
- PIE Delivery Baseline: `<baseline_id>`
- PIE Delivery Ask: `<ask_id>`
```

This keeps downstream work traceable without making PIE own downstream planning.
