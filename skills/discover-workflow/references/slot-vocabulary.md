# Slot Vocabulary

Known workflow slots. **Open-ended** — append new entries as `discover-workflow` mints them.

## Seed vocabulary

- **`new-feature`** — Plan a new feature from scratch (specs, plan). Triggers when starting a feature with no existing specs.
- **`implement-feature`** — Build code against existing specs. Triggers when the user says "implement / build / finish" a planned feature.
- **`bugfix`** — Reproduce the defect (write a failing test first when reasonable), diagnose root cause, fix, verify. Triggers on bug reports, failing tests, regression complaints.
- **`refactor`** — Restructure existing code without behaviour change. Tests must pass before and after. Triggers on cleanup, naming changes, extraction, deduplication.

## Naming rules

- Full words. No `-ing` suffix (e.g. `bugfix` not `bug-fixing`, `refactor` not `refactoring`).
- Kebab-case for multi-word names (e.g. `new-feature`, `code-review`, `seo-audit`).
- Verb-noun (`implement-feature`) or descriptive-noun (`new-feature`) shape. Match what the user would naturally say.
- No project prefix — `.ai/skills/<slot>/` path already disambiguates project scope.

## Open-ended

The seed list is illustrative, not exhaustive. Mint new slots when a session genuinely doesn't fit any existing one. Examples that may appear over time: `seo-audit`, `design-review`, `perf-optimization`, `accessibility-pass`, `migration`, `incident-response`, `dependency-upgrade`, `release`, `docs-pass`.

When minting a new slot:
1. Confirm the name with the user.
2. Append a new bullet to the seed list above (slot name + one-line description of what it covers and what triggers it).
3. Then proceed with the rest of the discover-workflow flow.
