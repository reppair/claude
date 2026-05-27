# Scripts and assets

Two less-common resource types: executable code (`scripts/`) and templates or files used in output (`assets/`).

## Scripts (`scripts/`)

Executable code (Python/Bash/etc.) for tasks needing deterministic reliability or repeatedly rewritten logic.

- **When to include**: same code is being rewritten repeatedly; deterministic output is needed.
- **Example**: `scripts/rotate_pdf.py` for PDF rotation.
- **Benefits**: token-efficient (need not be loaded into context), deterministic, can be executed directly.
- **Note**: Claude may still read scripts to patch them or adjust for the local environment.
- **Verify**: any script added must be run at least once to confirm it works.

## Assets (`assets/`)

Files used in output but not loaded into context.

- **When to include**: templates, images, icons, fonts, boilerplate code that the skill copies or modifies into final output.
- **Examples**: `assets/logo.png`, `assets/slides.pptx`, `assets/frontend-template/`, `assets/font.ttf`.
- **Benefits**: separates output resources from documentation; usable without loading into context.
