#### SVGs

- Keep a single master SVG per icon/logo as the source of truth
  - Regenerate PNG/ICO exports from it rather than hand-editing exported raster files when the design changes.
- For icons that should inherit a component's color (e.g., an icon next to accent-colored text), use fill="currentColor" or stroke="currentColor" inside the SVG instead of a hardcoded fill/stroke color, so it responds to CSS color the way text does.
- Run new/edited SVGs through an optimizer before committing 
  - svgo (bun-installable: bunx svgo input.svg -o output.svg) strips editor cruft (unused defs, excessive precision, comments) that design tools tend to leave in, often cutting file size substantially with no visual change
- Inline small, frequently-reused icons directly as components rather than <img>-referencing them, so currentColor and hover states work without extra plumbing
- For larger illustrations used once, a normal <img src="..."> referencing the optimized file is simpler and fine.
