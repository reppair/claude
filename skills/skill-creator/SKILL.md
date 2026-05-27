---
name: skill-creator
description: Create new skills, or make structural changes to existing ones. Use when authoring a skill from scratch, restructuring a skill, or deciding where new content belongs (body vs reference). Do NOT use for minor content edits that don't affect structure.
---

# Skill Creator

## Core principles

### Concise is key

The context window is a public good. Skills share it with the system prompt, conversation history, other skills' metadata, and the user's request.

**Default assumption: Claude is already very smart.** Only add context Claude doesn't already have. Challenge each piece of information: *"Does Claude really need this?"* Prefer concise examples over verbose explanations.

### Match degrees of freedom to the task

- **High freedom (text instructions)** — multiple valid approaches; context-dependent decisions.
- **Medium freedom (pseudocode, parameterised scripts)** — preferred pattern exists; some variation allowed.
- **Low freedom (specific scripts, few parameters)** — fragile operations; consistency critical.

## Anatomy

```
skill-name/
├── SKILL.md (required: YAML frontmatter + Markdown body)
└── (optional)
    ├── scripts/     — executable code for deterministic operations
    ├── references/  — docs loaded into context on demand
    └── assets/      — files used in output (templates, fonts, etc.)
```

### Frontmatter

`name` and `description` are the only allowed fields. The description is the only thing Claude reads to decide when the skill triggers — **all "when to use" information goes in the description, not the body** (the body is only loaded after the skill triggers).

Cover three things, in order:

1. **What** the skill does.
2. **When to fire** — explicit triggers, contexts, phrases.
3. **When NOT to fire** — disambiguation against sibling skills.

Be specific without bloating. Example: *"Create, edit, and extract content from .docx files including tracked changes and comments. Use when working with Word documents. Do NOT use for plain-text or Markdown files."*

### What NOT to include

No README.md, INSTALLATION_GUIDE.md, QUICK_REFERENCE.md, CHANGELOG.md, or other auxiliary docs. The skill contains only what an AI agent needs to do the job — no meta-context about how the skill was built, no setup instructions, no user-facing documentation.

## Progressive disclosure

Three levels load progressively: metadata always in context, SKILL.md body loads on trigger, bundled resources load on demand. Keep SKILL.md under 500 lines. When content is variant-specific (per framework, per domain, per pattern) or detailed reference material, extract to `references/`. See `references/progressive-disclosure.md` for patterns and examples.

## Creating a new skill

Four-step procedure. Follow in order, skipping only when clearly inapplicable.

### Step 1: Understand

Clarify usage with concrete examples. Skip only if usage patterns are already clearly understood. Otherwise ask the user concrete questions:

- What functionality should the skill support?
- Examples of how it would be used?
- What user phrasing should trigger it?

Avoid asking too many questions in a single message — start with the most important and follow up as needed. If available resolve using the `grill-me` skill.

### Step 2: Plan

For each example, ask: *"what reusable resource would help here?"* Three types to choose from:

- **Reference** — docs Claude should consult while working (schemas, conventions, API specs). This is the common case. See `references/references-guide.md`.
- **Script** — deterministic code that would otherwise be rewritten each time.
- **Asset** — a template or file the skill copies into the output (a Blade view, an image, a stub).

For scripts and assets, see `references/scripts-and-assets.md`.

Worked examples — *"to handle this kind of request, what would help?"*:

| Skill               | Example request                         | Repeated each time              | Bundled resource               |
|---------------------|-----------------------------------------|---------------------------------|--------------------------------|
| `big-query`         | *"how many users logged in today?"*     | table schemas re-discovered     | `references/schema.md`         |
| `pdf-editor`        | *"rotate this PDF"*                     | same rotation code              | `scripts/rotate_pdf.py`        |
| `invoice-generator` | *"generate an invoice for order #4821"* | invoice layout, logo, print CSS | `assets/invoice-template.html` |

### Step 3: Scaffold

Consult `references/scaffolding.md` for the directory steps and body-structure patterns to choose from — this is the default.

Skip when the user has been explicit about structure, references, and resources upfront (no scaffolding decisions left to make). In that case, `mkdir` the directory and write SKILL.md with the planned content directly.

### Step 4: Edit

Start with the reusable resources identified in Step 2, then write SKILL.md. Some resources require user input — e.g. a `brand-guidelines` skill needs the user to provide logos/templates for `assets/` or policy docs for `references/`. Ask when the resource isn't something Claude can produce alone.

**Writing style:** imperative/infinitive form throughout.

**Design patterns:**
- `references/workflows.md` — multi-step sequences and conditional logic.
- `references/output-patterns.md` — output templates and quality standards.

## Updating an existing skill

Before editing, read the target SKILL.md and any references the change touches. The existing skill is the working template — match its heading depth, list style, voice, and section grouping.

No creation procedure needed. Before applying the change, run it past these checks:

1. **Conventions** — does the result still satisfy Core principles (concise!), Anatomy, Frontmatter, What NOT to include, and Progressive disclosure?
2. **Length** — does the change push SKILL.md past 500 lines, or close enough to warrant an extraction now?
3. **New resource type** — is the change introducing `scripts/`, `assets/`, or a first `references/` file? Consult `references/scripts-and-assets.md` or `references/references-guide.md`.
4. **Inline vs reference** — is the new content variant-specific or detailed reference material? Default to inline; consult `references/progressive-disclosure.md` if extracting.
5. **Frontmatter** — if touched, re-validate against the Frontmatter section above.
6. **Cross-links** — if renaming, splitting, or removing a reference, update every link to it in SKILL.md.

If the existing skill already violates a convention unrelated to the current change, flag it to the user — don't silently fix it as part of a scoped update.
