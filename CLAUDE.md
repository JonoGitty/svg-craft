# SVG Craft

A Claude Code skill for creating production-ready SVG images for websites.

## What this project is

This is a Claude Code skill pack (`/svg-craft`) that enables Claude to:
- Create SVG icons, illustrations, logos, and decorative elements from scratch
- Trace/recreate SVGs from raster images (PNG, JPG, PDF)
- Fetch websites and match their colour palette and design language
- Optimise SVG markup for production use
- Add CSS/SMIL animations to SVGs
- Ensure accessibility (roles, titles, contrast)

## Project structure

```
.claude/skills/svg-craft/
  SKILL.md       # Skill definition (slash command)
  CONTEXT.md     # Domain knowledge (SVG reference, tracing strategies, theme extraction)
examples/        # Example SVG outputs (for reference and testing)
```

## Key tools used

- **Read** — View source images (PNG/JPG/PDF) visually for tracing
- **WebFetch** — Retrieve website HTML/CSS for theme extraction
- **Write** — Output SVG files
- **Agent** — Delegate sub-tasks (e.g. research a site's design system)

## Conventions

- SVGs use `viewBox` and omit fixed `width`/`height` for responsiveness
- CSS custom properties (`var(--name, fallback)`) for theme adaptability
- Accessibility: `role="img"`, `<title>`, and contrast compliance
- File size targets: icons < 1 KB, illustrations < 5 KB, complex < 20 KB
