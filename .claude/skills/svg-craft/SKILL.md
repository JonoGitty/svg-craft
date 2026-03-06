---
name: svg-craft
description: Create, optimise, and theme SVG images for websites — trace from PNGs/PDFs, match site colour palettes, build icons/illustrations/logos, manage a reusable snippet library
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
- A favicon, og:image, or social preview graphic
- To browse or add to their SVG snippet library

## Capabilities

### 1. Create SVGs from scratch
Write clean, hand-coded SVG markup. Prefer semantic shapes (`<rect>`, `<circle>`, `<path>`) over opaque path dumps. Use `viewBox` for scalability. Check the snippet library first for reusable parts.

### 2. Trace / emulate from raster images (PNG, JPG, PDF)
- Use the **Read** tool to view the source image visually
- For **geometric / icon-style** images: recreate by hand using SVG primitives
- For **organic / complex** images: use **potrace** via Bash if installed, or advise the user to install it. Do NOT attempt to hand-code organic shapes (animals, faces, maps) — it doesn't work.
- For **website screenshots**: use **Playwright** to capture, then analyse

### 3. Match website themes
- Use **WebFetch** to retrieve the target website's HTML/CSS
- Optionally use **Playwright** (`npx playwright screenshot`) to capture a visual screenshot for context
- Extract the colour palette from CSS custom properties, meta tags, and class patterns
- Apply the extracted palette to SVG output using CSS custom properties for adaptability

### 4. Optimise existing SVGs
- Use **svgo** via Bash if installed: `svgo input.svg -o output.svg`
- Manual cleanup if svgo isn't available (see CONTEXT.md checklist)

### 5. Animate SVGs
- CSS animations for hover effects, loading spinners, transitions
- SMIL for path morphing and complex sequenced animations
- Check `library/animations/` for ready-made animation snippets

### 6. Generate common assets
- **Favicon**: Use `library/templates/favicon.svg` as base. Design at 32x32, test at 16x16.
- **OG image**: Use `library/templates/og-image.svg` as base. 1200x630 standard.
- **Icon sets**: Use `library/templates/icon-set-base.svg` for consistent 24x24 icons.

### 7. Snippet library management
The library lives at `C:/ai/svg-craft/library/` with these categories:
- `gradients/` — reusable gradient defs
- `patterns/` — repeating patterns (dots, grid, noise)
- `icons/` — icon collection
- `shapes/` — common shapes (badges, frames, splats)
- `animations/` — animation snippets (spinner, pulse, fade)
- `palettes/` — colour palettes extracted from websites
- `templates/` — full SVG templates (favicon, og-image, icon-set)

**When generating SVGs:**
1. Check the library first for reusable snippets
2. After generating, ask the user if they want to save any reusable parts to the library
3. When the user asks to "browse the library" or "show me what's available", list the contents with descriptions

**When the user wants to build their library:**
- Ask what categories they want to add to (gradients, patterns, icons, shapes, animations)
- Generate snippets and save with the standard header format (see library/README.md)
- Each snippet needs: `<!-- snippet: name -->`, `<!-- tags: ... -->`, `<!-- usage: ... -->`

## Approach

1. **Understand the brief** — What is the SVG for? Where will it appear? What style?
2. **Check the library** — Scan `library/` for relevant snippets, gradients, or templates
3. **Gather references** — Read any provided images with Read. Fetch target website with WebFetch. Screenshot with Playwright if needed.
4. **Extract the palette** — If matching a site theme, pull colours from CSS and save to `library/palettes/`
5. **Design the SVG** — Write clean SVG code. Use library snippets where available.
6. **Preview** — Generate a preview using `tools/preview.html` (copy it next to the SVGs, open in browser). Show at multiple sizes on light and dark backgrounds.
7. **Optimise** — Run `svgo` if installed, or manual cleanup
8. **Offer to save** — Ask the user if any reusable parts (gradients, shapes, patterns) should be saved to the library
9. **Deliver** — Write the SVG file to the requested location. Provide usage notes.

## External tools (check availability, offer to install)

| Tool | Check | Install | What it does |
|------|-------|---------|-------------|
| svgo | `which svgo` | `npm i -g svgo` | Optimise SVGs — strips junk, simplifies paths |
| potrace | `which potrace` | See tools/README.md | Trace raster images to vector paths |
| sharp-cli | `which sharp` | `npm i -g sharp-cli` | Convert/resize images before tracing |
| playwright | `npx playwright --version` | `npx playwright install chromium` | Screenshot websites for visual analysis |

Before using any external tool, check if it's installed. If not, explain what it does and ask the user if they want to install it. See `tools/README.md` for full docs.

## Other skills to combine with

- **/simplify** — After generating SVGs, run simplify to review code quality
- **claude-api** — If building an app that generates SVGs dynamically via Claude

$ARGUMENTS
