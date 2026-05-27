# Conventions for produced skills

Load this file at step 7 of `discover-workflow`, before invoking `skill-creator`. It defines the rules a produced workflow skill must follow.

## Location

Write the skill to `.ai/skills/<slot>/SKILL.md`. Do **not** create a `.claude/skills/<slot>` symlink — `php artisan boost:install` (or `boost:update`) wires custom skills automatically. If the project has no `.ai/skills/` directory (non-Laravel-Boost project), fall back to `.claude/skills/<slot>/SKILL.md` directly.

## After writing the skill

If the skill was written under `.ai/skills/`, run `php artisan boost:install` via Bash so Boost registers it. If under `.claude/skills/`, no further action — the harness picks it up automatically.

Boost regenerates the project-root `CLAUDE.md` on each run — treat it as a build artifact. Never edit it by hand; if project conventions need to change, update the source (the skill itself, or rules in `.claude/rules/`).

## Frontmatter `description`

Keep it concise — one paragraph at most. Cover three things, in order:

1. **What** the skill does.
2. **When to fire** — explicit phrases, file patterns, slash-command form.
3. **When NOT to fire** — disambiguation against sibling skills.

The description is the only thing Claude reads to decide whether to trigger the skill. Long descriptions waste tokens on every conversation and dilute the trigger signal.

## Referencing other skills

When a step uses another skill, **name it inline at the step where it's needed**. The executing agent invokes it via the Skill tool when the step is reached.

Two flavours:

- **Mid-flow reference** — the current skill keeps going after the named skill returns. Example: *"run the `coverage` skill to verify tests, then update the changelog."*
- **Terminal handoff** — the current skill ends by handing off to the next. Canonical example: `new-feature` defines specs and a plan, then ends with *"Continue with the `implement-feature` skill to build."*

Rules:

- Never embed another skill's content in the body — name it, don't copy it.
- Never pre-load sibling skills earlier than the step that uses them. Both Skill tool and Read leave the loaded body in context for the rest of the session — loading early bloats context with content not yet relevant.
- Reference a skill at every step where it's used; the executing agent will load the body once and keep it in context.
