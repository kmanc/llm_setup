---
name: frontend-design
description: Use this skill when the user asks to build a new UI or reshape or update an existing one
---


# Frontend design

- Approach this as a design lead known for giving every project a distinct visual identity that could not be mistaken for any other's 
- Make deliberate, opinionated choices about palette, typography, and layout that are specific to this brief, and take aesthetic risk if justified


## Ground your designs in the subject matter

- If the brief does not identify what the product or subject matter is, identify it yourself before designing, and confirm with me
  - Come up with one concrete subject, the design's audience, and the design's primary job, as a proposal
- If you have any context about what you're building the UI for, use that as a starting place
  - The subject's industry, subject matter, materials, and vernacular are where distinctive visual choices come from
  - Build with the brief's real content and subject matter throughout


## Design principles

- Use typography to set the personality of the page
  - Choose typefaces deliberately, not the default families you would reach for on any other project, and set a clear type scale following the default guidance of The Elements of Typographic Style with intentional weights, widths, and spacing
  - When type is used as a headline or visual element, use the type treatment itself as an active part of the design, not a neutral delivery vehicle for the content
- Don't use different typeface for display or headline text and body content: use one family or two
  - If using two, make them clearly distinct
- Default to line lengths of less than 80 characters
  - Exception: Serif typefaces can have slightly longer line lengths; give serif body text slightly more line-height than a sans-serif.
- Do not use the dollowing typographic treatments; they are the commonest tells of a generated page and hence indicate slop:
  - Accenting just a single word or phrase in a headline, like putting one word in italic/bold or a different color
  - Using all caps for labels
  - Adding unnecessary typographic labels above content

- Use visual structure to convey and present information
  - Leverage structural devices like outlines, borders, numbering, eyebrows, dividers, labels, etc., to encode useful information about the content rather than decorate it
  - Don't use numbered markers (01 / 02 / 03) unless the content actually is a sequence — like a stepped process or a timeline

- Use non-user-triggered motion sparingly and deliberately, and only to draw attention
  - A single orchestrated moment — one page-load sequence or one reveal — lands better than scattered effects; fade-and-slide-up entrances on each section and hover transitions on every card are the generic default and read as AI-generated
- Motion that answers a person's action (opening, expanding, confirming) is welcome when it shows what changed


## Writing in design

- Use words in a UI to make it easier to understand and use
  - Words are design content, not decoration so bring the same intentionality and minimalism to copywriting that you would bring to spacing and color
  - Before writing anything, ask what the design needs to say, and how it can best be said to help the person navigate the experience
- Write from the end user's perspective
  - Name things by what users will understand in simple language, not by how the system is built
    - For example, a user manages notifications, not webhook config
  - Describe what something is or does in plain terms rather than selling it. Being specific and legible to new users is always better than being clever.
- Use active voice as default. A CTA says exactly what happens when it is used: "Save changes," not "Submit." 
- Maintain action names through their whole flow
  - For example a button that says "Publish" should produce toast that says "Published." The vocabulary of an interface is the signposting for someone navigating the product
- Utilize cohesion and consistency to help users learn their way around
- Keep the tone conversational: plain verbs, sentence case, no filler, with tone matched to the brand and the audience
- Let each written element do exactly one job.


## Assets

- Default to SVG for anything that benefits from scaling or recoloring: icons, logos, illustrations, UI glyphs
  - Load [svg.md](references/assets/svg.md) for more info on using SVGs as assets
- Create and organize assets for modern use, but keep compatibility backups
  - Load [static.md](references/assets/static.md) for more info on what assets to have and where to store them

## Process

#### Plan

For calibration, AI-generated design right now clusters around some traits:
- A warm cream background (near #F4F1EA) with a high-contrast serif display and a terracotta or warm-clay accent (often near #D97757 — Anthropic's own Claude-interaction accent, so on a user's brief it reads as a tell);
- A near-black background with a single bright acid-green or vermilion accent;
- A broadsheet-style layout with hairline rules, zero border-radius, and dense newspaper-like columns;
- The SaaS-card kit: content chopped into identical rounded cards, one border-radius on everything regardless of hierarchy, the same soft grey shadow (rgba(0,0,0,.1)) under each, and gradient washes as decoration;
- Template chrome that appears whatever the subject: a tracked-out ALL-CAPS eyebrow label above every heading; meta strings joined with middle dots ('A · B · C'); labels built as 'WORD — fragment' with a spaced em dash; tinted near-black (#0B0B0B, #111) standing in for black; a monospace face for small data labels; a '→' appended to link and button text.

Where the brief pins down a visual direction, follow it exactly; the brief's own words always win, including when it asks for one of these looks
Where it leaves an axis free, don't spend that freedom on one of the above defaults

First, brainstorm a short design plan based on the client's design brief: create a compact token system with color, type, layout, and principles
- Color: describe the core base palette as 4–6 named hex values
- Type: the typefaces and their roles
- Layout: a layout concept, using one-sentence prose descriptions and ASCII wireframes to ideate and compare. Include alignment guidance; should the content be left aligned, center aligned, justified?
- Principles: the high-level guidance for what makes this page unique.

#### Review

Review that plan against the brief: if any part of it reads like the generic default you would produce for any similar page rather than a choice made for this specific brief — revise that part noting what you changed and why

#### Confirm

After you're satisfied with the review, mock up a few pages using the plan with dummy data in a `/ui_mockups` directory (that you may have to create) and run it by me for final approval. I will either approve the design plan or provide feedback on what to change. Either way, delete the mock-ups when this step is done.
- Load [process.md](references/mockups/process.md) for more info on the mockup procedure

#### Build

Be careful when structuring CSS selector specificities as it's easy to generate CSS classes that cancel each other out (especially with a type-based selector like .section and an element-based selector like .cta). This can happen often with padding/margin between sections.

#### Critique

- Spend your boldness in one place. Let one element be the memorable thing, keep everything around it quiet and disciplined, and cut any decoration that does not serve the brief
- Build to a quality floor without announcing it: responsive down to mobile, visible keyboard focus, reduced motion respected, visually accessible, harmonious color palettes
- Critique your own work as you build, and make sure that the output matches the plan
