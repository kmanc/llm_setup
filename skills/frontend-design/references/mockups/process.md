# The mockup-first workflow, in detail

## Why a static HTML detour before build

- The whole point is cheapness of change. 
  - A single `.html` file with inline `<style>` has no routing, no component boundaries, no build step, and no framework conventions to fight — moving a section, swapping
a font, or trying a second layout direction is a copy-paste-and-edit away
- Once the same content exists as routed components with scoped styles and real data wiring, the identical change touches more files and carries more risk of breaking something unrelated
- Getting sign-off before that point means disagreements get resolved while they're still cheap.
- This isn't about distrust of the design — it's the same reason a hired designer shows a client a comp before opening a code editor. Alignment is faster to establish on something the client can react to in ten seconds than through a written token plan alone

## What goes in the mockup

- The full set of CSS custom properties from the token plan, defined once in a `<style>` block at the top (`:root { --color-bg: ...; --font-size-lg: ...; }`), then used throughout 
  - This makes the mockup double as a live preview of the actual token file you'll extract into the real project later
- Real or realistic copy pulled from the brief, not `lorem ipsum` — generic filler text makes it much harder for the person reviewing to judge whether the design actually serves the content
- The real layout structure (header, sections, footer, nav, etc) even if some content is a reasonable placeholder (e.g., a testimonial that will later come from a CMS) — structural decisions are exactly what needs sign-off before routing/components get built around them
- Basic interactive states worth showing statically where relevant (a hover style, a focus outline) even though there's no real interactivity yet — CSS `:hover`/`:focus` work fine in a plain HTML file.

## What doesn't need to be in the mockup

- Real data fetching, form submission, auth, routing between pages — none of that is what's being aligned on at this stage. A second page can be a second `<section>` in the same file with a comment marking the "page break," or a second sibling HTML file, whichever is faster to review as a whole.
- Pixel-perfect responsive behavior at every breakpoint — get the primary viewport right and note the responsive approach in prose; fully finishing responsive behavior is reasonable to defer to the Svelte build once the desktop/primary direction is approved.

## Minimal template shape

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Mockup — [project name]</title>
  <style>
    :root {
      /* token plan lives here, e.g.: */
      --color-bg: #...;
      --color-surface: #...;
      --color-text: #...;
      --color-text-muted: #...;
      --color-accent: #...;
      --color-border: #...;
      --font-size-base: 1rem;
      --font-size-lg: 1.25rem;
      --font-size-2xl: 2rem;
      --space-1: 0.25rem;
      --space-2: 0.5rem;
      --space-4: 1rem;
      --space-8: 2rem;
    }
    * { box-sizing: border-box; }
    body {
      margin: 0;
      background: var(--color-bg);
      color: var(--color-text);
      font-family: <chosen font stack>;
    }
    /* component-ish sections below, named like the Svelte components they'll become */
  </style>
</head>
<body>
  <!-- header, hero, sections, footer — real structure, real copy -->
</body>
</html>
```

Naming the CSS sections/classes after the components they'll eventually become (`.site-header`, `.hero`, `.pricing-card`) makes the translation step later closer to a mechanicalcopy-over than a re-design

## Presenting it

Show the mockup as a renderable artifact where the environment supports it (an HTML artifact the user can view directly), or otherwise save it and point to the file. Ask directly for a decision — approve, or specific changes — rather than a vague "let me know what you think," and treat "looks good" as the actual gate. If the user asks for a structural change, it's much cheaper to make it here than after a build has started.
