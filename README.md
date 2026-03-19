# SVG Craft

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill for creating production-ready SVG images for websites. Trace from PNGs/PDFs, match site themes, optimise markup, and build a reusable snippet library over time.

## What it does

SVG Craft adds a `/svg-craft` slash command to Claude Code. When invoked, Claude gains specialised knowledge and workflows for:

- **Creating SVGs from scratch** — icons, logos, illustrations, decorative elements
- **Tracing raster images** — recreate PNGs/JPGs/PDFs as clean vector SVGs (using potrace for organic shapes)
- **Matching website themes** — fetch a site, extract its colour palette, and apply it to your SVGs
- **Generating web assets** — favicons, Open Graph images, icon sets from built-in templates
- **Optimising SVG markup** — strip editor junk, simplify paths, reduce file size with svgo
- **Animating SVGs** — CSS keyframes and SMIL animations (spinners, fades, morphs)
- **Managing a snippet library** — save and reuse gradients, patterns, shapes, and palettes across projects

## Installation

Clone (or fork) this repo into your project:

```bash
git clone https://github.com/JonoGitty/svg-craft.git
```

Then copy the skill into any Claude Code project that needs it:

```bash
# Copy the skill definition into your project
cp -r svg-craft/.claude/skills/svg-craft/ your-project/.claude/skills/svg-craft/
```

Or keep the full repo around to use the snippet library, preview tool, and examples alongside the skill.

### Optional external tools

These are not required but significantly extend what the skill can do:

| Tool | Purpose | Install |
|------|---------|---------|
| **svgo** | Optimise SVGs (strip metadata, simplify paths) | `npm i -g svgo` |
| **potrace** | Trace raster images to vector paths | [potrace.sourceforge.net](https://potrace.sourceforge.net/) |
| **sharp-cli** | Convert/resize images before tracing | `npm i -g sharp-cli` |
| **playwright** | Screenshot websites for visual theme analysis | `npx playwright install chromium` |

Quick install for the npm-based tools:

```bash
npm install -g svgo sharp-cli
npx playwright install chromium
```

The skill checks for tool availability at runtime and will suggest installation when a tool would help.

## Usage

Inside Claude Code, invoke the skill with a description of what you need:

```
/svg-craft a minimalist camera icon for a photography site
```

```
/svg-craft trace this logo from logo.png and match the colour scheme at https://example.com
```

```
/svg-craft create a favicon for my project using purple and dark grey
```

```
/svg-craft optimise all SVGs in ./assets/icons/
```

```
/svg-craft browse my snippet library
```

The skill follows a structured workflow: understand the brief, check the library for reusable parts, gather references (images, website palettes), generate the SVG, preview it at multiple sizes, optimise, and offer to save reusable parts back to the library.

## Project structure

```
.claude/skills/svg-craft/
  SKILL.md              Skill definition (slash command entry point)
  CONTEXT.md            Domain knowledge (SVG reference, tracing strategy, theming)
library/
  gradients/            Reusable gradient definitions
  patterns/             Repeating patterns (dots, grid, noise)
  icons/                Icon collection (24x24 standard)
  shapes/               Common shapes (badges, frames, splats)
  animations/           Animation snippets (spinner, pulse, fade)
  palettes/             Colour palettes extracted from websites
  templates/            Starter files (favicon, og-image, icon-set)
tools/
  preview.html          Drag-and-drop SVG previewer (multiple sizes and backgrounds)
  README.md             External tool documentation
examples/               Example SVG outputs
```

## Snippet library

The `library/` directory is a growing collection of reusable SVG parts. Each snippet is a standalone `.svg` file with a comment header:

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

**Included starter snippets:**

- **Gradients** — sunset, purple-cyan, dark-depth
- **Patterns** — dots, grid, noise
- **Shapes** — badge, frame, paint-splat
- **Animations** — spinner, pulse, fade-in-up
- **Templates** — favicon (32x32), og-image (1200x630), icon-set-base (24x24)

The skill checks the library before generating and offers to save new reusable parts after each generation.

## Preview tool

`tools/preview.html` is a drag-and-drop SVG previewer. Open it in a browser and drop your SVG files in to see them rendered at 160px, 64px, 32px, and 16px on light, dark, and mid-tone backgrounds. This is useful for checking that icons remain legible at small sizes and that colours work across contexts.

## Design conventions

SVGs generated by this skill follow these conventions:

- **Responsive sizing** — `viewBox` is always set; fixed `width`/`height` are omitted so SVGs scale to their container
- **Theme adaptability** — colours use CSS custom properties (`var(--name, fallback)`) so SVGs adapt to light/dark modes
- **Accessibility** — `role="img"` and `<title>` on informational SVGs; `aria-hidden="true"` on decorative ones; colour contrast compliance
- **File size targets** — icons under 1 KB, illustrations under 5 KB, complex graphics under 20 KB
- **Semantic markup** — prefers `<rect>`, `<circle>`, `<ellipse>` over opaque path data where possible

## Known limitations

Hand-coded SVG works well for geometric and designed shapes but poorly for organic forms. The skill is upfront about this:

| Good at | Bad at |
|---------|--------|
| Geometric logos and icons | Animals, faces, realistic plants |
| Compositions from SVG primitives | Geographic outlines (maps) |
| Gradients, patterns, colour theming | Photo-realistic tracing |
| CSS/SMIL animations | Complex curved silhouettes |
| Accessibility and optimisation | |

For organic shapes, the skill uses **potrace** (or recommends installing it) rather than attempting to hand-code what it cannot do well.

## License

This project is provided as-is for use with Claude Code.
