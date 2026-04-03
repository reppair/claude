---
name: update-specs
description: >-
  Reviews specs/ and ensures any changes from the current session are properly reflected, especially design decisions.
  Use after completing a feature, fix, or refactor, or after a /grill-me session that produced design decisions.
---

# Update Specs

Update specification files in `specs/` to reflect design decisions and changes from the current session.

## Flow

### 1. Check for specs

If `specs/` does not exist, say so and stop:

```
No specs/ directory found in this project — nothing to update.
```

### 2. Determine scope

- **Conversation context exists** — scan the current conversation for design decisions, architectural choices, and behavioral changes. This is the scope.
- **Arguments provided** (e.g., `/update-specs auth flow decisions`) — use the arguments as scope. If anything is unclear, invoke `/grill-me` to clarify.
- **No context, no arguments** — invoke `/grill-me` to figure out what to update and how.

### 3. Scan existing specs

List filenames in `specs/` first. Based on filenames alone, identify which specs *might* be affected by the scope. Only read the contents of those likely matches — don't read every spec unless explicitly asked to.

#### 3.1. Progress tracking
Look for `specs/*-status.md` / `specs/tasks.md` or other similar files that may be present for as means to track progress, read them, understand the structure and content, how they relate to the scope, and update them as necessary.

### 4. Present and confirm

Only show specs that need action. If nothing needs updating, say so and stop.

```
## Specs to update
- specs/foo.md — brief reason
- specs/bar.md (new) — brief reason why a new spec is appropriate
```

Wait for confirmation before making changes. The user may add, remove, or adjust items.

### 5. Update

For each confirmed spec:
- Read the current file
- Update only the sections affected by the scope
- Specs should describe the final design, not the change history
- Keep the existing spec's style and structure

## Rules

- **Scope strictly** — only update specs related to the current session's decisions. Don't rewrite unrelated specs.
- **Final state, not changelog** — specs describe how things should work, not what changed.
- **Design decisions first** — prioritize capturing the *why* behind decisions, not just the *what*.
- **Progess** - if the project has a spec file to track progress or tasks, update it to track progress
- **Suggest, don't create silently** — if a new spec file seems appropriate, suggest it with `(new)` and ask. Don't create automatically.
- **Existing specs only get scoped edits** — don't rewrite an entire spec when only one section is affected.
- **When in doubt, grill** — if the scope is ambiguous or you're unsure what a spec should say, ask rather than guess.
