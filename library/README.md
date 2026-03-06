# SVG Snippet Library

Your personal collection of reusable SVG patterns, components, and palettes.
Built up over time as you use the /svg-craft skill.

## Structure

```
library/
  gradients/      - Reusable gradient definitions
  patterns/       - Repeating patterns (dots, lines, grids, noise)
  icons/          - Icon collection (24x24 viewBox standard)
  shapes/         - Common shape templates (badges, banners, frames)
  animations/     - Animation snippets (spinners, transitions, hover effects)
  palettes/       - Colour palettes extracted from websites
  templates/      - Full SVG templates (favicon, og-image, icon-set)
```

## How to use

When running /svg-craft, you can:
- **Browse the library** — ask to see what's available
- **Use a snippet** — reference any snippet by name and it gets dropped into your SVG
- **Save to library** — after generating an SVG, save any reusable parts back here
- **Build your collection** — the skill will offer to save useful patterns as you work

## Adding snippets

Each snippet is a standalone `.svg` file with a comment header:

```xml
<!-- snippet: gradient-purple-cyan -->
<!-- tags: gradient, purple, cyan, brand -->
<!-- usage: <rect fill="url(#gradient-purple-cyan)" .../> -->
<svg xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="gradient-purple-cyan" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0%" stop-color="#7c6ff7"/>
      <stop offset="100%" stop-color="#22d3ee"/>
    </linearGradient>
  </defs>
</svg>
```
