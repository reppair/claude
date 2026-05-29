---
name: implement-feature
description: |
  Implement a planned feature from the project's specs. Triggers when the user says "implement task X", "let's build N.N", "resume from .handoff.md", or hands you a known task ID. Do NOT use for: planning a fresh feature from scratch, reproducing and fixing a defect, or restructuring existing code without behaviour change.
---

# Implement Feature

Portable spec-driven build loop. Specs are the contract; the codebase becomes the record once shipped.

This skill assumes a **Laravel + Pest + Laravel Boost** project and a **spec-driven** workflow — but **not** a single fixed spec layout. Each project declares its own SDD shape (this user's hand-rolled `specs/` tree in one project, a community framework like spec-kit in another). Learn the project's shape from its **always-on project guidelines** (Boost compiles `.ai/guidelines/*` into the root `CLAUDE.md`; also check `.claude/rules/`), then map the generic steps below onto that project's actual files. Where this skill names a concept (task list, decisions log, user-intent artifact), resolve it to the project's concrete file via those guidelines — do not assume a path.

**Polyscope environment:** some steps below behave differently when running under Polyscope. You're in Polyscope when **CWD is under `~/.polyscope/clones/`** *or* **the system prompt declares Polyscope context**. Polyscope-gated rules are collected in the final section — check there whenever a step points to it.

**Keep a running judgment-call log** throughout the work — every deliberate decision the agent makes that a reviewer would want to know about, each with a one-line *why*. Common cases (not exhaustive): something dropped from the spec, something added that wasn't asked for, a choice between viable alternatives, a workaround for a quirk, a deferred follow-up, a naming/shape decision, an assumption made when the spec was silent. If in doubt, log it.

The log is **mutable**: when a later pipeline step disproves an earlier entry, *remove* it (don't strike through). Re-read the whole log right before opening the PR and prune anything later steps invalidated. The pruned log feeds the PR body in §8 so the reviewer sees every still-standing deliberate deviation in one place.

## 1. Pick up the work

**Always read:**

- `.handoff.md` (repo root) if present — task + prior-session decisions.
- The project's **task list / planned-work index** — find it via the project guidelines (e.g. `specs/TASKS.md` in a `specs/`-style project, or the framework's equivalent). Find the task entry; it indexes into everything else — follow any references it makes.

**Reference as needed:** if the project ships index files for its specs/docs (e.g. `specs/README.md`, `docs/README.md`), consult them for what each file covers. Load only what the task needs — don't pull in the whole spec set up front.

**Blocker — no captured user intent:** if the task has no matching **user-intent artifact** (user story, acceptance criteria, spec narrative — whatever this project's SDD uses to record user-voice intent), stop and invoke the `grill-me` skill to draft one with the user before starting. Intent artifacts capture the *why* that task rows don't. The session may surface spec updates beyond it — task tweaks, new decisions, glossary additions, parked-issue entries — apply those too, following the project's guideline conventions. Before moving on to implementation, ask the user whether to use the `/commit` command to commit the spec updates and start a fresh session for the build — usually worth it if the grilling burned significant context.

## 2. Plan and track

**Branch check first — before creating any task or touching code.** Verify the branch is `feature/<kebab-name>` matching the task scope, and resolve it now — correcting an untracked branch is free; correcting after `git push -u origin` is annoying.

- On `master` / `main` / `develop` → `git checkout -b feature/<kebab-name>`.
- On a non-conforming feature branch → `git branch -m feature/<kebab-name>`.

Then use `TaskCreate` to break the work into discrete units. One task per shippable file/class is usually right. Update status as you go.

**Granular pipeline tasks are a hard gate before §5.** Before advancing to §5, expand `TaskCreate` to cover *every individual item* in §5 and §6 — each skill invocation, each `laravel-simplifier` subagent pass, each Bash command, the `CHANGELOG` edit, and the end-of-run reconciliation. One task per item; never a single "run the pipeline" bucket — a bucket hides silent skips. This list is also the source for the §7 reconciliation table.

**No silent skips.** Every §5/§6 task must end `completed` with either the step's actual output *or* a one-line `skipReason` when environmentally blocked. "Feels disproportionate" / "diff is small" are **not** valid skip reasons — only genuine environmental blockers are (e.g. `skipReason: PhpStorm IDE not connected to this workspace`).

If breaking the work down surfaces gaps, contradictions, or unclear areas in the spec, **stop and invoke the `grill-me` skill** to resolve them with the user before continuing. Apply any spec updates that fall out (same handling as the §1 blocker — intent artifact, decisions, glossary, parked issues; offer to commit and restart fresh if context burn warrants it).

## 3. Implement following project patterns

Lean on the installed global skills for scaffolding, file layout, and idiomatic patterns — `laravel-best-practices`, `pest-testing`, `livewire-development`, `fluxui-development`, `fortify-development`, `tailwindcss-development`, plus `phpstorm-plugin:php-project-guide` for PHP conventions, and any Filament-specific guidance the project ships (e.g. a Filament Blueprint section in its guidelines). They cover artisan `make:*` generation, file placement, and how things look and work in this stack.

Consult `laravel-boost`'s `search-docs` MCP tool for version-specific Laravel/Filament docs and artisan flags before inventing patterns. Always pass `--no-interaction` on `make:*` calls.

Do **not** restate spec content in code comments — link in the PR if needed.

**Follow the project's own conventions, don't impose generic ones.** Architectural choices (e.g. action-class vs service-class, repository usage, where business logic lives) are project-specific:

1. **Infer** from siblings — read neighbouring files in the target directory and match their shape, and check the project guidelines (always-on `CLAUDE.md` / `.ai/guidelines/`, `.claude/rules/`) for a stated preference.
2. **Ask** only if the choice is *material* and *unstated* — siblings are silent or conflict, and getting it wrong would be costly to undo. Use `grill-me` or a direct question.
3. **Offer to persist** the answer into the project's guidelines (a Boost `.ai/guidelines/` file, or wherever the project records conventions) so the question doesn't recur. Don't write it without the user's nod.

Run the **`laravel-simplifier` subagent** on each unit as you go — once on the implementation before writing tests (so tests track the simplified shape), then again on the tests once they're written (agents tend to over-complicate tests too). Every later step (review, coverage, verify) operates on cleaned-up code on both sides.

When a simplifier pass finds nothing worth applying, capture its rationale (1-2 lines) in the judgment-call log — it documents which invariants are protecting the current shape against "obvious" simplifications. E.g. *"Simplifier: no changes — the guarding decision is load-bearing; the action deliberately mirrors an existing sibling."*

## 4. Test aggressively

Every new method, closure, conditional gets a test. Common examples (not exhaustive — apply the same rigor to whatever the feature adds):

- Action class → `handle()` happy path, validation edges, state guards
- Enum with methods → every case in datasets
- Filament widget → render + state for each branching condition
- Filament page → action exists, action behavior, validation, smoke tests

Run targeted first, then full suite in parallel at the end:

```bash
php artisan test --compact --filter=NameTest
php artisan test --compact --parallel
```

## 5. Pre-ship review pipeline

Run **in order** before commit. Each is an installed skill. Each has its own granular task from §2 and closes with output or a one-line `skipReason` (no silent skips).

1. **`phpstorm-plugin:php-code-review`** — PhpStorm inspections on each new PHP file. Cheap linter-style pass first, so the heavier reviewers below don't re-flag the same noise. **Gated on PhpStorm IDE connection:** invoke only if the PhpStorm IDE MCP is connected to this workspace (`mcp__ide__*` tools registered). If not, skip with `skipReason: PhpStorm IDE not connected to this workspace`. Do **not** substitute a manual review — that masquerades as having run the step.
2. **`coverage`** — CRAP analysis. Close any real gaps now — filling them often surfaces logic the rest of the pipeline should evaluate too.
3. **`code-review`** with `--effort high` — correctness bugs
4. **`security-review`** — OWASP-class issues on the diff
5. **`verify`** — invoke the **`verify` skill**; let it drive the structured check list (golden path + edge cases + error states + period-switching). Do **not** reach for `mcp__chrome-devtools__*` tools yourself and call a single golden-path screenshot "verified" — the skill encodes the edge/error checks a manual pass misses. (Under Polyscope, see the final section for how `verify` chooses its driver.)

Address every CONFIRMED / KEEP finding before moving on. PLAUSIBLE findings get a judgement call — and if a later step disproves a logged finding, remove it from the judgment-call log (per the log rules at the top).

## 6. Wrap-up rituals

In order:

1. `vendor/bin/pint --dirty --format agent`
2. Run the full suite one more time
3. Run the **`update-specs`** skill — syncs the project's specs to reality: flips the shipped task to done, promotes shipped patterns to the project's decisions log, and updates any other specs touched (glossary, parked issues, etc.), following the project's guideline conventions.
4. Run the **`update-docs`** skill — adds or extends the project's feature docs. Skip for non-shape changes (column tweaks, copy, bug fixes).
5. **`CHANGELOG.md`** — append entry under today's date header, user-facing language, focus on what's now visible
6. Delete `.handoff.md` if it was used — session-local, not project content

**Always invoke the `update-specs` and `update-docs` skills** for steps 3 and 4 — hand-editing spec/doc files as a substitute is forbidden. The skill owns the process and decides whether to defer; the agent doesn't. If a skill returns "no changes needed", that *is* the recorded outcome for its granular task.

## 7. End-of-run reconciliation gate

After all §5–§6 tasks complete and **before any commit / PR action or closing prose**, emit a per-step reconciliation table sourced from the granular `TaskCreate` list (§2). One row per step: ✅ Done / ⚠️ Partial / ❌ Skipped, the actual tool / skill / subagent / command name it invoked (not a paraphrased label), the parameters that materially affected the outcome (e.g. `code-review --effort high`), and a note or `skipReason`. List both what ran **and** what was expected but didn't, in the same view.

| Step | Status | Notes |
|---|---|---|
| `php-code-review` | ✅ | 0 findings |
| `coverage` | ⚠️ | CRAP gap on `handle()` branch; added test |
| `security-review` | ❌ | `skipReason: env-blocked, MCP unavailable` |

This table is the **decision point** — never auto-commit. After emitting it, stop and ask the user: *"Proceed with `/commit` + `/pr`, or address [specific row] first?"* Only on approval move to §8.

## 8. Commit and PR

On approval, invoke the **`/commit` command**, then the **`/pr` command** — do not compose the PR body inline or call `gh pr create` directly.

`/pr` already drafts `## Summary`. Pass it arguments to append two **extra** blocks in addition to that default:

- `## Notes` — the judgment-call log accumulated since §1, post-retraction (pruned per the log rules at the top).
- `## Pre-ship pipeline` — the reconciliation table from §7.

Commits must use `--signoff` (the `/commit` command handles this). The `/pr` command creates the PR via `gh pr create`; push `-u origin` first if the branch isn't tracked. (Branch naming was already settled in §2.)

## When something blocks

- Unclear scope → invoke the `grill-me` skill against the spec
- Schema lookup → `mcp__laravel-boost__database-schema`
- Doc question → `mcp__laravel-boost__search-docs`

## Polyscope environment

These rules apply only under Polyscope (detection in the intro). Outside Polyscope, ignore them.

**Artefact location.** Use `.context/` (workspace root) for verify screenshots, intermediate diagrams, and session droppings. Ensure `/.context` is in `.gitignore` as a **root-only pattern** (`/.context`, not `**/.context`) so only the workspace-root directory is ignored. These artefacts are session-only — deleting the Polyscope workspace removes them, which is fine; they're not project content.

**`verify` driver choice.** The `verify` skill (§5.5) decides its driver based on the check. Default to the **Polyscope integrated preview** where possible — it handles cookies, auth, and most flows. Fall back to **Chrome DevTools** (`mcp__chrome-devtools__*`) only when a check can't be done via preview or fails there — *and only if those tools are registered in this session* (they may be absent, e.g. Polyscope on a remote VPS). Save screenshots to `.context/` either way.
