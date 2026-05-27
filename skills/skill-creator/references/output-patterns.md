# Output patterns

Patterns for producing consistent output from a skill.

## Template pattern

Provide templates for output format. Match the level of strictness to your needs.

**For strict requirements (like API responses or data formats):**

```markdown
## Report structure

ALWAYS use this exact template:

# [Title]

## Executive summary
[Overview]

## Key findings
- [Finding with supporting data]
- ...

## Recommendations
1. [Actionable recommendation]
2. ...
```

**For flexible guidance (when adaptation is useful):**

```markdown
## Report structure

Here is a sensible default format, but use your best judgment:

# [Analysis Title]

## Executive summary
[Overview]

## Key findings
[Adapt sections based on what you discover]

## Recommendations
[Tailor to the specific context]

Adjust sections as needed for the specific analysis type.
```

## Examples pattern

For skills where output quality depends on seeing examples, provide input/output pairs:

```markdown
## Commit message format

Match these examples:

`Added user authentication with JWT tokens` →
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```
`Fixed bug where dates displayed incorrectly in reports` →
```
fix(reports): correct date formatting in timezone conversion

Use UTC timestamps consistently across report generation
```
Format: `type(scope): description`, then explanation.
```

Examples help Claude understand the desired style and level of detail more clearly than descriptions alone.
