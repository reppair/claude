# Scaffolding a new skill

Directory layout, frontmatter, and body-structure patterns for new skills.

## Steps

### 1. Create the skill directory

```bash
mkdir -p <path>/<skill-name>
```

Check first that `<path>/<skill-name>` doesn't already exist. If it does, ask the user before overwriting.

`<skill-name>` requirements:
- Hyphen-case identifier (e.g. `data-analyzer`).
- Lowercase letters, digits, and hyphens only.
- Max 40 characters.
- Must match the directory name exactly.

### 2. Write SKILL.md with frontmatter and a minimal body

```markdown
---
name: <skill-name>
description: [TODO: what the skill does + when to trigger + when not to trigger]
---

# <Skill Title>

[TODO: body content — fill in during Step 4 of the skill-creator process]
```

Do not paste in a long opinionated body template at this stage — the body structure depends on the skill's purpose (see "Body structure patterns" below).

### 3. No empty directories or placeholder files

Only create resource directories and files when you have real content for them.

## Body structure patterns

When designing the SKILL.md body (Step 4 of skill-creator), pick the structure that fits the skill's purpose. Most skills mix patterns.

### Workflow-based — sequential processes

Best when there are clear step-by-step procedures.

> Overview → Workflow Decision Tree → Step 1 → Step 2 → Step 3...

Example: a DOCX-editing skill with "Read → Modify → Save".

### Task-based — tool collections

Best when the skill offers multiple discrete operations.

> Overview → Quick Start → Task 1 → Task 2 → Task 3...

Example: a PDF skill with merge / split / extract / sign.

### Reference/guidelines — standards or specifications

Best for brand styling, coding standards, or requirements.

> Overview → Guidelines → Specifications → Usage...

Example: a brand-styling skill.

### Capabilities-based — integrated systems

Best when features are interrelated and the user composes them.

> Core Capabilities → Capability 1 → Capability 2 → Capability 3...

Example: a project-management skill with planning, tracking, and reporting features.
