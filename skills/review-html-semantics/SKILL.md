---
name: review-html-semantics
description: Review a page template's HTML structure for semantic correctness, SEO, accessibility, and spec compliance. Renders a structure tree and discusses issues interactively. Use when reviewing a Blade/HTML/JSX template, layout, or component — standalone, or as part of a broader code or content review.
---

Review the given page template's HTML structure against the HTML spec, SEO best practices, and screen reader accessibility.

## Process

1. **Read the template** — read the full file, understand each block's purpose.
2. **Render the structure tree** — output an ASCII tree showing every element with its tag, role, and a short label. Flag issues inline with (!) markers:

```
<div>                              <- root
+-- <div>                          <- page heading (<h1>)
+-- <section aria-label="...">     <- product listing (ok)
+-- <section>                      <- promo banner (! no heading, not a content section)
+-- <div>                          <- period switcher (<nav> inside, ok)
```

3. **List issues** — for each (!) flag, explain:
   - What's wrong (spec/SEO/a11y)
   - Why it matters (screen readers, crawlers, or spec compliance)
   - Recommended fix
4. **Discuss** — walk through each issue one at a time. Wait for the user's decision before fixing.
5. **Apply fixes** — only after agreement. Run tests after changes.

## Rules to check

### Sectioning elements (`<section>`, `<article>`, `<aside>`, `<nav>`)
- `<section>` is for distinct, thematic content blocks — not wrappers, not UI chrome
- Every `<section>` should have a heading (`<h1>`-`<h6>`) or `aria-label`/`aria-labelledby`
- Don't use `<section>` for: layout wrappers, decorative elements, single images/banners, form controls, navigation chrome
- `<nav>` must have `aria-label` when multiple `<nav>` elements exist on the page

### Heading hierarchy
- One `<h1>` per page, at the page level (not nested inside a `<section>`)
- Headings should not skip levels (`<h1>` -> `<h3>` without `<h2>`)
- A heading used as a UI label (e.g. "Choose payment period") is not a section heading — consider if `<h2>` is appropriate or if it should be a `<span>`/`<p>` with styling

### Accessibility
- Interactive regions need labels: `<nav aria-label>`, `<section aria-label>`
- `aria-label` for sections without visible headings
- `aria-labelledby` when a visible heading exists (prefer over `aria-label` to avoid duplication)
- Decorative images: `alt=""`; content images: descriptive `alt`

### SEO
- `<h1>` should contain the primary page keyword
- `<section>` elements help search engines understand content boundaries
- Don't wrap non-content (nav, controls, banners) in `<section>` — it dilutes the semantic signal

### Common mistakes
- `<section>` used as a `<div>` for styling/spacing
- `<div>` used where `<section>` is warranted (distinct content block with no semantic tag)
- `<h2>` on UI labels that aren't section headings
- Multiple `<nav>` without distinguishing `aria-label`
- `<section>` wrapping the entire page instead of individual content blocks