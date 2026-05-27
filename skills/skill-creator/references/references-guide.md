# References (`references/`)

Documentation and reference material loaded into context only when needed.

- **When to include**: docs Claude should consult during work — schemas, API specs, conventions, detailed workflow guides.
- **Examples**: `references/schema.md` for database table schemas; `references/api_docs.md` for API specs.
- **Benefits**: keeps SKILL.md lean; loaded only when relevant.
- **Best practices**:
  - If a file is large (>10k words), include grep search patterns in SKILL.md so Claude can navigate.
  - Avoid duplication. Each piece of information lives in either SKILL.md or a reference file, never both. Prefer references for detail and keep SKILL.md procedural.
