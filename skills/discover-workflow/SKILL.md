---
name: discover-workflow
description: Extract repeatable workflow patterns from the current session and persist them as project-level skills under `.ai/skills/<slot>/`. Use at end of session, on a resumed session with loaded context, or when the user says "discover workflow", "capture this workflow", "save this process as a skill", or invokes /discover-workflow. Accepts an optional slot argument (e.g. new-feature, bugfix, refactor). Do NOT use for: capturing facts (memory), one-off solutions, or patterns already covered by an installed global skill.
---

# Discover Workflow

Capture verbs (procedures), not nouns (facts). Prefer multiple narrow skills over one omnibus. Write to `.ai/skills/<slot>/` (fall back to `.claude/skills/<slot>/` if `.ai/skills/` doesn't exist).

If the session has no relevant context and the user provided none, invoke `grill-me` to interview them about the workflow before proceeding.

## Execution flow

1. **Identify slot(s).** Accept the optional argument or infer from session content. Consult `references/slot-vocabulary.md` for known slots and naming rules; if none fit, mint a new slot, confirm with the user, and append it to the vocabulary file.

2. **Handle multi-workflow sessions.** Long sessions may span multiple slots (e.g. a `bugfix` that became a `refactor`). Group captured changes per slot — each becomes a separate proposal.

3. **Extract content.** For each slot, scan for: ordered steps actually taken, decisions made, project-specific conventions, branching logic. Apply the refusal criteria below.

4. **Apply memory-vs-skill split.** Verbs → skill body (e.g. *"run migrations, then seed"*). Nouns → memory (e.g. *"app is deployed"*). Skill links to memory via pointer, never copies. Note new facts for the memory update in step 7.

5. **Diff against existing skill.** If the target directory exists, prepare a diff of additions/changes. Otherwise mark for creation.

6. **STOP — Present grouped proposals per slot and await approval.** For each slot show: action (create/update), 1–3 line summary, proposed body, and what was skipped with reasons. User may approve some slots and reject others.

7. **For each approved slot:** load `references/produced-skill-conventions.md`, then invoke the `skill-creator` skill — it handles both new-skill creation and structural updates to an existing skill under those conventions. After all approved skills are written, update project memory under `~/.claude/projects/<project-dir>/memory/` for any new facts — the executing agent already has the resolved path in its system context.

## Refusal criteria

Filter these out; tell the user briefly what was skipped — they may override.

1. **Mid-session corrections** — keep only the chosen approach, never the discarded one.
2. **One-off solutions** — work tied to a specific file, commit, PR, or incident. Generalise or drop.
3. **Generic framework patterns** — already covered by an installed global skill (check the skills already available in your session). Capture only project-specific deviations.
4. **Already documented** — anything in memory, `CLAUDE.md`, or project specs/docs. Link, don't duplicate.
5. **Exploratory dead-ends** — investigated then abandoned.
6. **Speculative future work** — belongs in an issue tracker, not a workflow skill.

## Memory vs skill

- **Memory = nouns.** Facts, preferences, constraints. Lives in `~/.claude/projects/<project-dir>/memory/`.
- **Skill = verbs.** Ordered procedures. Lives in `.ai/skills/<slot>/`.
