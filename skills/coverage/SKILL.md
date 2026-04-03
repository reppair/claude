---
name: coverage
description: >-
  Audits test coverage for recent work. Maps what was added, changed, and deleted against what was actually tested.
  Outputs a compact summary so the developer can spot gaps at a glance. Use after completing a feature, fix, or refactor.
---

# Coverage

Audit the work done in this conversation (or the specified scope) and produce a test coverage map.

## Steps

1. **Gather changes** — Review conversation context (or `git diff` if needed). Group related changes together (e.g., a repository + its model binding = one entry). Skip obvious non-testables (docs, config, views without logic).

2. **Gather tests** — Identify tests added, changed, or deleted. Map each to the code it covers.

3. **Output the map** — Only show what matters:

```
## Test Coverage Map

### Covered
- SomeNewClass (added) — SomeNewClassTest (N tests, replaces OldClassTest)
- ExistingModel events (changed) — SomeNewClassTest (relevant subset)
- SomeComponent (changed) — ComponentTest (N tests)

### Gaps
- some-feature (changed) — no test coverage
- OldAuthTests (6 tests, deleted) — not yet replaced

Covered: 3/5 | Gaps: 2
Deleted without replacement: 1
Recommendation: Add auth tests before committing
```

## Rules

- Be honest about gaps — the goal is visibility, not a green checkmark.
- Skip non-testables entirely — don't list docs, config, or logic-free views.
- Replaced tests are noted inline under Covered, not as a separate section.
- A deleted test without replacement goes under Gaps — always flagged.
- Keep it scannable. One line per logical unit of work, not per file.
