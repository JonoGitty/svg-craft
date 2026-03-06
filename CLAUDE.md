# SVG Craft

A Claude Code skill for creating production-ready SVG images for websites.

## What this project is

This is a Claude Code skill pack (`/svg-craft`) that enables Claude to:
- Create SVG icons, illustrations, logos, and decorative elements from scratch
- Trace/recreate SVGs from raster images (using potrace for organic shapes)
- Fetch websites and match their colour palette and design language
- Generate favicons, OG images, and icon sets from templates
- Optimise SVG markup with svgo or manual cleanup
- Add CSS/SMIL animations to SVGs
- Manage a reusable snippet library that grows over time

## Project structure

```
.claude/skills/svg-craft/
  SKILL.md         # Skill definition (slash command)
  CONTEXT.md       # Domain knowledge (SVG reference, tracing, theming, tools)
library/
  gradients/       # Reusable gradient definitions
  patterns/        # Repeating patterns (dots, grid, noise)
  icons/           # Icon collection (24x24 standard)
  shapes/          # Common shapes (badges, frames, splats)
  animations/      # Animation snippets (spinner, pulse, fade)
  palettes/        # Colour palettes extracted from websites
  templates/       # Starter files (favicon, og-image, icon-set)
tools/
  preview.html     # Drag-and-drop SVG preview at multiple sizes/backgrounds
  README.md        # External tool docs (svgo, potrace, sharp, playwright)
examples/          # Example SVG outputs
```

## External tools (optional, enhance the skill)

| Tool | Purpose | Install |
|------|---------|---------|
| svgo | Optimise SVGs | `npm i -g svgo` |
| potrace | Trace raster images to vector | See tools/README.md |
| sharp-cli | Image conversion/resize | `npm i -g sharp-cli` |
| playwright | Website screenshots | `npx playwright install chromium` |

## Key conventions

- SVGs use `viewBox` and omit fixed `width`/`height` for responsiveness
- CSS custom properties (`var(--name, fallback)`) for theme adaptability
- Accessibility: `role="img"`, `<title>`, and contrast compliance
- File size targets: icons < 1 KB, illustrations < 5 KB, complex < 20 KB
- Library snippets use standard header: `<!-- snippet: name -->`, `<!-- tags: ... -->`, `<!-- usage: ... -->`
- Always preview at multiple sizes before delivering
- Always offer to save reusable parts to the library after generating

## Known limitations

Hand-coded SVG is great for geometric/designed shapes but bad for organic forms (animals, faces, maps). For those, use potrace or real data sources. See CONTEXT.md for the full decision tree.
