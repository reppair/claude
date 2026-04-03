---
name: optimize-image
description: Optimize an <img> tag for Google PageSpeed best practices
user_invocable: true
---

Optimize the given `<img>` tag in context of its page. Read the file containing the image to understand its purpose and surrounding layout.

## Checklist

Apply each point. Skip what's already correct.

1. **`loading`** — `eager` for above-fold / hero / LCP images. `lazy` for everything else.
2. **`fetchpriority`** — `high` on the LCP image only. Omit otherwise.
3. **`decoding`** — `async` on all images.
4. **`width` / `height`** — Always set explicit intrinsic dimensions to prevent layout shift (CLS). Read the actual image file to get real pixel dimensions. Only skip if Tailwind classes constrain **both** width and height.
5. **`alt`** — Descriptive, concise. Empty `alt=""` only for purely decorative images.
6. **`srcset` / `sizes`** — Add responsive variants if the image renders at different sizes across breakpoints and source files exist. Don't generate image files.
7. **Format** — Read the actual image file to verify its format/MIME type. Prefer `webp` or `avif`. Flag if a non-modern format (png/jpg) is used and a modern alternative exists.

## Output

Show only the changed `<img>` tag with a one-line note per change made.