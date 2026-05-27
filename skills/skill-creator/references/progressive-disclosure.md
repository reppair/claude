# Progressive disclosure patterns

Patterns for organising SKILL.md and `references/` so context use stays proportional to actual need.

## Three-level loading

1. **Metadata (name + description)** — always in context (~100 words).
2. **SKILL.md body** — loads when the skill triggers.
3. **Bundled resources** — load on demand (scripts can run without being read into context).

## Extension references

Body covers the common path and any concepts the agent needs to follow it; references go deeper on specific topics, loaded only when the user's task touches them. Multiple references may load across a task — or none.

Two equivalent layouts:

**One "Advanced features" block** — collect deeper-topic links in a single section near the end:

```markdown
# API fetcher

## Quick start
Run `scripts/fetch.py <endpoint>` to fetch and parse JSON.

## Advanced features
- **Authentication**: see [auth.md](auth.md)
- **Pagination**: see [pagination.md](pagination.md)
- **Rate limiting and retries**: see [retries.md](retries.md)
```

**Per-section drill-down** — each topic section keeps its basics inline and links out for advanced bits:

```markdown
# Git workflow

## Committing
`git add` + `git commit -m`. Use conventional commit prefixes.
- **Signing commits**: see [signing.md](signing.md)

## Branching
`git checkout -b <name>` then `git push -u origin <name>`.
- **Branch protection**: see [branch-protection.md](branch-protection.md)
- **Interactive rebase**: see [rebase.md](rebase.md)
```

## Variant references

Body picks one of several references based on context (domain, framework, version). Exactly one reference loads per task — whichever matches the user's situation. Unrelated context never loads.

```
cloud-deploy/
├── SKILL.md (workflow + provider selection)
└── references/
    ├── aws.md
    ├── gcp.md
    └── azure.md
```

Deploying to AWS loads only `aws.md`.

## Important guidelines

- **Avoid deeply nested references.** Keep references one level deep from SKILL.md — all reference files should link directly from SKILL.md.
- **Structure longer reference files.** For files >100 lines, include a table of contents at the top so Claude can preview the scope.
