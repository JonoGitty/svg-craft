# SVG Craft Tools

External tools that extend the SVG Craft skill. Install what you need.

## Tools Overview

| Tool | What it does | Install |
|------|-------------|---------|
| **svgo** | Optimise SVGs — strips metadata, simplifies paths, reduces file size | `npm install -g svgo` |
| **potrace** | Trace raster images (PNG/BMP) to SVG vector paths | `npm install -g potrace` or system package |
| **sharp-cli** | Convert/resize images (PNG/JPG/WebP) before tracing | `npm install -g sharp-cli` |
| **playwright** | Screenshot websites for visual theme analysis | `npm install -g playwright && npx playwright install chromium` |

## svgo — SVG Optimiser

Strips editor junk, simplifies paths, minifies. The single most useful tool here.

```bash
# Optimise a single file (overwrites)
svgo input.svg -o output.svg

# Optimise all SVGs in a folder
svgo -f ./icons/ -o ./icons-optimised/

# Preview what would change (dry run)
svgo input.svg --pretty -o -

# Custom config (keep viewBox, remove dimensions)
svgo input.svg -o output.svg --config='{"plugins":[{"name":"removeDimensions"}]}'
```

**When to use:** After every SVG generation. Run svgo on the output before delivering.

## potrace — Raster to Vector Tracing

Converts bitmap images to clean SVG paths. Much better than hand-coding organic shapes.

```bash
# Basic trace (input must be BMP or PBM — convert first with sharp)
potrace input.bmp -s -o output.svg

# With sharp-cli to convert PNG first
sharp -i input.png -o input.bmp && potrace input.bmp -s -o output.svg

# Adjust detail level
potrace input.bmp -s -o output.svg -t 5          # less detail (simpler paths)
potrace input.bmp -s -o output.svg -t 1          # more detail (complex paths)

# Posterise for colour tracing (multiple layers)
potrace input.bmp -s -o output.svg --color '#7c6ff7'
```

**When to use:** When the user provides a raster image (PNG/JPG) and wants an accurate vector trace. This is how you handle organic shapes (animals, people, maps) that hand-coding can't do.

## sharp-cli — Image Conversion

Resize and convert images before tracing or for generating raster exports of SVGs.

```bash
# Convert PNG to BMP for potrace
sharp -i logo.png -o logo.bmp

# Resize before tracing (smaller = simpler paths)
sharp -i photo.png --resize 400 -o photo-small.png

# Convert to greyscale (better for silhouette tracing)
sharp -i logo.png --greyscale -o logo-grey.png
```

## playwright — Website Screenshots

Take full-page screenshots for visual theme analysis. More reliable than parsing CSS alone — you see what the user sees.

```bash
# Quick screenshot of a URL
npx playwright screenshot --browser chromium "https://example.com" screenshot.png

# Full page screenshot
npx playwright screenshot --browser chromium --full-page "https://example.com" full.png

# Specific viewport size
npx playwright screenshot --browser chromium --viewport-size "1280,720" "https://example.com" desktop.png

# Mobile viewport
npx playwright screenshot --browser chromium --viewport-size "390,844" "https://example.com" mobile.png
```

**When to use:** When matching a website's theme. Screenshot it, Read the screenshot to see the visual design, then also WebFetch the HTML/CSS to extract exact colour values. The combination gives you both visual context and precise data.

## Installation Script

Run this to install all tools at once:

```bash
npm install -g svgo sharp-cli
npx playwright install chromium
```

Note: `potrace` may need a system-level install on Windows:
- Download from https://potrace.sourceforge.net/
- Or use `choco install potrace` if you have Chocolatey
