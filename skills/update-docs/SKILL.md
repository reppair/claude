---
name: update-docs
description: >-
  Keeps docs/ in sync with the current state of the codebase. Scoped to the current task's changes — not the entire
  branch or refactor. Use after completing a feature, fix, or refactor to ensure documentation reflects reality.
---

# Update Docs

Update documentation in `docs/` to reflect recent changes. Scoped to what was just worked on, not the entire codebase.

## Flow

### 1. Determine scope

- **Conversation context exists** — scan the current conversation for what was added, changed, and deleted. This is the scope.
- **Arguments provided** (e.g., `/update-docs about auth flow and session handling`) — use the arguments as scope. If anything is unclear, invoke `/grill-me` to clarify.
- **No context, no arguments** — invoke `/grill-me` to figure out what to update and how.

### 2. Scan existing docs

List filenames in `docs/` first. Based on filenames alone, identify which docs *might* be affected by the scope. Only read the contents of those likely matches — don't read every doc unless explicitly asked to (e.g., "scan all files").

### 3. Present and confirm

Only show docs that need action. If nothing needs updating, say so and stop.

```
## Docs to update
- docs/foo.md — brief reason
- docs/bar.md (new) — brief reason why a new doc is appropriate
```

Wait for confirmation before making changes. The user may add, remove, or adjust items.

### 4. Update

For each confirmed doc:
- Read the current file
- Update only the sections affected by the scope
- Docs should describe the final state, not the change history
- If a `docs/style-guide.md` exists, follow it. Otherwise, keep docs concise and informative — tables over prose, don't restate what the code says, no "Usage" sections showing how to call a method, 1-2 sentence paragraphs, flat bullet lists for files and tests.

## Rules

- **Style guide first** — if the project has `docs/style-guide.md`, read it before writing or editing any doc. If not, default to: concise, informative, tables over prose, no code that just mirrors what's in the file.
- **Scope strictly** — only update docs related to the current task. A frontend rebuild doesn't mean rewriting `docs/webhooks.md` just because some views were deleted.
- **Final state, not changelog** — docs describe how things work now, not what was removed or migrated.
- **Suggest, don't create silently** — if a new doc file seems appropriate, suggest it with `(new)` and ask. Don't create automatically.
- **Existing docs only get scoped edits** — don't rewrite an entire doc when only one section is affected.
- **When in doubt, grill** — if the scope is ambiguous or you're unsure what a doc should say, ask rather than guess.
