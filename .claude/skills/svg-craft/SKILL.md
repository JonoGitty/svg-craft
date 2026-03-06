---
name: svg-craft
description: Create, optimise, and theme SVG images for websites — trace from PNGs/PDFs, match site colour palettes, build icons/illustrations/logos
user-invocable: true
argument-hint: "[describe the SVG you need, or provide an image/URL to work from]"
allowed-tools: Bash, Read, Edit, Write, Grep, Glob, WebFetch, Agent
---

# SVG Craft

Create production-ready SVG images for websites: icons, illustrations, logos, decorative elements, and traced artwork from raster sources.

## When to use

Use /svg-craft when the user needs:
- A new SVG icon, illustration, logo, or decorative element
- An SVG traced/recreated from a PNG, JPG, or PDF reference image
- SVG colour palette matched to an existing website's theme
- SVG optimisation (simplify paths, reduce file size, clean markup)
- Animated SVGs (CSS or SMIL animations)
- Responsive/accessible SVGs with proper viewBox, roles, and titles
- Batch generation of a consistent icon set

## Capabilities

### 1. Create SVGs from scratch
Write clean, hand-coded SVG markup. Prefer semantic shapes (`<rect>`, `<circle>`, `<path>`) over opaque path dumps. Use `viewBox` for scalability.

### 2. Trace / emulate from raster images (PNG, JPG, PDF)
- Use the **Read** tool to view the source image visually
- Analyse the shapes, colours, layout, and style
- Recreate the image as clean SVG markup, simplifying where appropriate
- For complex images, break into layers: background, main subject, details
- Preserve the visual intent rather than pixel-perfect tracing

### 3. Match website themes
- Use **WebFetch** to retrieve the target website's HTML/CSS
- Extract the colour palette (primary, secondary, accent, background, text colours)
- Identify the site's design language (rounded vs sharp, minimal vs detailed, flat vs gradient)
- Apply these styles to the SVG output so it fits seamlessly

### 4. Optimise existing SVGs
- Remove unnecessary metadata, comments, and editor cruft
- Simplify paths and merge redundant groups
- Convert inline styles to attributes where smaller
- Ensure proper `viewBox`, remove fixed `width`/`height` for responsiveness
- Add `role="img"` and `<title>` for accessibility

### 5. Animate SVGs
- CSS animations for hover effects, loading spinners, transitions
- SMIL for path morphing and complex sequenced animations
- Keep animations performant (prefer `transform` and `opacity`)

## Approach

1. **Understand the brief** — What is the SVG for? Where will it appear? What style?
2. **Gather references** — Read any provided images (PNG/JPG/PDF) with the Read tool. Fetch the target website with WebFetch if a URL is given.
3. **Extract the palette** — If matching a site theme, pull colours from CSS custom properties, key element styles, or visible branding.
4. **Design the SVG** — Write clean SVG code. Use descriptive IDs, logical grouping with `<g>`, and CSS classes for theming.
5. **Review and refine** — Check the SVG visually by reading it back. Optimise the markup. Ensure accessibility attributes are present.
6. **Deliver** — Write the SVG file to the requested location. Provide usage notes (recommended size, dark-mode variants, animation triggers).

## SVG Best Practices

```xml
<!-- Always include viewBox, omit width/height for responsiveness -->
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" role="img">
  <title>Descriptive title for accessibility</title>
  <!-- Use CSS custom properties for easy theming -->
  <style>
    .primary { fill: var(--svg-primary, #1a1a2e); }
    .accent  { fill: var(--svg-accent, #e94560); }
  </style>
  <g id="icon-name">
    <!-- Semantic shapes over raw paths where possible -->
  </g>
</svg>
```

### Colour strategy
- Use CSS custom properties (`var(--name, fallback)`) so SVGs adapt to light/dark modes
- When matching a website, map: brand colour -> primary fill, accent -> highlights, background -> negative space
- Provide both a themed version and a standalone version with hardcoded colours

### File size targets
- Simple icons: < 1 KB
- Illustrations: < 5 KB
- Complex traced artwork: < 20 KB (simplify if larger)

## Other skills to combine with

- **/simplify** — After generating SVGs, run simplify to review code quality
- **claude-api** — If building an app that generates SVGs dynamically via Claude

$ARGUMENTS
