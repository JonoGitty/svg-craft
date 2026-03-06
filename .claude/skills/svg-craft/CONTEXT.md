# SVG Craft — Domain Knowledge

## SVG Fundamentals

### Coordinate system
- `viewBox="minX minY width height"` defines the internal coordinate space
- Omit `width`/`height` attributes to let the SVG scale responsively
- If fixed size is needed, set both `viewBox` and `width`/`height`

### Core elements
| Element | Use |
|---------|-----|
| `<rect>` | Rectangles, rounded rectangles (`rx`/`ry`) |
| `<circle>` | Circles (`cx`, `cy`, `r`) |
| `<ellipse>` | Ellipses (`cx`, `cy`, `rx`, `ry`) |
| `<line>` | Single lines |
| `<polyline>` | Connected line segments (open) |
| `<polygon>` | Closed shapes from points |
| `<path>` | Complex shapes (Bezier curves, arcs) |
| `<text>` | Text (embed fonts or convert to paths for logos) |
| `<g>` | Grouping (apply transforms/styles to children) |
| `<defs>` | Reusable definitions (gradients, patterns, clips) |
| `<use>` | Instance a defined element |
| `<symbol>` | Like `<g>` but with its own `viewBox` |

### Path commands (d attribute)
- `M x y` — Move to
- `L x y` — Line to
- `H x` — Horizontal line
- `V y` — Vertical line
- `C x1 y1 x2 y2 x y` — Cubic Bezier
- `S x2 y2 x y` — Smooth cubic Bezier
- `Q x1 y1 x y` — Quadratic Bezier
- `T x y` — Smooth quadratic Bezier
- `A rx ry rotation large-arc sweep x y` — Arc
- `Z` — Close path
- Lowercase = relative coordinates

### Gradients
```xml
<defs>
  <linearGradient id="grad1" x1="0%" y1="0%" x2="100%" y2="100%">
    <stop offset="0%" stop-color="#e94560"/>
    <stop offset="100%" stop-color="#0f3460"/>
  </linearGradient>
  <radialGradient id="grad2" cx="50%" cy="50%" r="50%">
    <stop offset="0%" stop-color="#fff"/>
    <stop offset="100%" stop-color="#000"/>
  </radialGradient>
</defs>
<rect fill="url(#grad1)" .../>
```

### Filters (use sparingly — performance cost)
```xml
<defs>
  <filter id="shadow">
    <feDropShadow dx="2" dy="2" stdDeviation="2" flood-color="#00000033"/>
  </filter>
</defs>
```

### Clipping and masking
```xml
<defs>
  <clipPath id="circle-clip">
    <circle cx="50" cy="50" r="40"/>
  </clipPath>
</defs>
<image clip-path="url(#circle-clip)" .../>
```

---

## Tracing Raster Images — Strategy

When given a PNG/JPG/PDF to recreate as SVG:

1. **Read the image** with the Read tool to see it visually
2. **Identify the major shapes** — background, primary forms, detail elements
3. **Choose the right approach:**

### Decision tree: hand-code vs potrace

| Image type | Approach |
|-----------|----------|
| Simple icon, geometric logo | Hand-code with SVG primitives |
| Text-based logo | Hand-code with `<text>` or simple `<path>` |
| Complex organic shape (animal, face, plant) | **Use potrace** — do NOT hand-code |
| Map or geographic outline | **Use real GIS data** (GeoJSON -> SVG) — do NOT hand-code |
| Photo or detailed illustration | **Use potrace** for silhouette, or advise raster format |
| Abstract/geometric illustration | Hand-code, break into layers |

### Using potrace (preferred for organic shapes)

```bash
# Check if available
which potrace

# Convert PNG to BMP first (potrace needs bitmap input)
sharp -i input.png --greyscale -o input.bmp

# Basic trace to SVG
potrace input.bmp -s -o output.svg

# Control detail: -t threshold (higher = simpler, lower = more detail)
potrace input.bmp -s -o output.svg -t 3

# Then clean up with svgo
svgo output.svg -o output-clean.svg
```

### Using Playwright for screenshots

```bash
# Screenshot a website for visual analysis
npx playwright screenshot --browser chromium "https://example.com" screenshot.png

# Full page
npx playwright screenshot --browser chromium --full-page "https://example.com" full.png

# Specific viewport
npx playwright screenshot --browser chromium --viewport-size "1280,720" "https://example.com" desktop.png
npx playwright screenshot --browser chromium --viewport-size "390,844" "https://example.com" mobile.png
```

Then Read the screenshot to visually analyse the site's design before extracting palette.

### KNOWN LIMITATIONS — Be upfront about these

**This skill is BAD at hand-coding:**
- Organic shapes from memory (animals, human figures, realistic plants)
- Geographic outlines (continents, countries)
- Photo-realistic tracing
- Complex curved silhouettes

**This skill is GOOD at hand-coding:**
- Geometric logos, icons, and brand marks
- Compositions from SVG primitives (rect, circle, ellipse, line, polygon)
- Clean path work with straight lines and simple curves
- Gradients, patterns, and colour theming
- CSS/SMIL animations
- Accessibility and optimisation

**When you hit a limitation, use external tools (potrace, real data) instead of struggling.**

---

## Website Theme Extraction — Full Workflow

### Step 1: Visual capture (if Playwright available)
```bash
npx playwright screenshot --browser chromium "https://target.com" /tmp/site.png
npx playwright screenshot --browser chromium --viewport-size "390,844" "https://target.com" /tmp/site-mobile.png
```
Read the screenshots to understand the visual design language.

### Step 2: Fetch HTML/CSS
Use WebFetch to get the page source. Look for:

1. **CSS custom properties** in `:root` or `html`:
   ```css
   :root {
     --bg-base: #1a1a1e;
     --accent: #7c6ff7;
   }
   ```

2. **Framework class patterns:**
   - Tailwind: `bg-blue-500`, `text-gray-100`
   - Bootstrap: `.btn-primary`, `.bg-dark`
   - Custom: inspect the actual property values

3. **Meta tags:**
   ```html
   <meta name="theme-color" content="#0f172a">
   ```

4. **Font stacks** — match in SVG `<text>` elements

5. **Border-radius values** — rounded (8px+) = rounded SVG shapes, sharp (0-2px) = crisp edges

6. **Dark mode** — `prefers-color-scheme` media queries, `.dark` class variants

### Step 3: Build palette
```
Primary:    #______  (brand / headers / CTA)
Secondary:  #______  (supporting elements)
Accent:     #______  (highlights / links)
Background: #______  (page background)
Surface:    #______  (card / panel background)
Text:       #______  (body text)
Muted:      #______  (secondary text / borders)
```

### Step 4: Save palette to library
Save as `library/palettes/sitename.svg`:
```xml
<!-- palette: sitename -->
<!-- extracted: YYYY-MM-DD -->
<!-- source: https://example.com -->
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 700 50">
  <rect x="0" width="100" height="50" fill="#primary"/>
  <rect x="100" width="100" height="50" fill="#secondary"/>
  <rect x="200" width="100" height="50" fill="#accent"/>
  <rect x="300" width="100" height="50" fill="#background"/>
  <rect x="400" width="100" height="50" fill="#surface"/>
  <rect x="500" width="100" height="50" fill="#text"/>
  <rect x="600" width="100" height="50" fill="#muted"/>
</svg>
```

---

## Snippet Library Usage

The library at `C:/ai/svg-craft/library/` contains reusable SVG parts.

### Categories
| Folder | Contents |
|--------|----------|
| `gradients/` | Gradient `<defs>` blocks — drop into any SVG |
| `patterns/` | Repeating patterns (dots, grid, noise texture) |
| `icons/` | User's icon collection (24x24 standard) |
| `shapes/` | Badges, frames, paint splats, decorative elements |
| `animations/` | Spinner, pulse, fade-in, hover effects |
| `palettes/` | Colour palettes extracted from websites |
| `templates/` | Starter files: favicon, og-image, icon-set-base |

### Snippet format
Every snippet has a comment header:
```xml
<!-- snippet: name-here -->
<!-- tags: comma, separated, tags -->
<!-- usage: brief usage instruction -->
```

### When to check the library
- **Before generating**: scan for relevant gradients, patterns, shapes
- **After generating**: offer to save reusable parts back to the library
- **When user asks**: list available snippets with `Glob` on `library/**/*.svg`

### When to grow the library
After each SVG generation, ask the user:
> "Want to save any of these to your library? I can extract the [gradient/pattern/shape/animation] for reuse."

Only save if the user says yes. Tag everything properly.

---

## Preview Workflow

Always generate a preview so the user can see the SVG at multiple sizes before finalising.

### Quick method: open directly
```bash
start "" "path/to/output.svg"
```

### Full comparison method
1. Copy `tools/preview.html` next to the SVG files
2. Open it in browser — drag and drop the SVGs in
3. It shows each SVG at 160px, 64px, 32px, 16px on light, dark, and mid backgrounds

### For multiple concepts
Generate a comparison HTML page (like the bcg-compare pages in examples/) showing all variants side by side at multiple sizes on multiple backgrounds.

---

## Favicon Generation

1. Start from `library/templates/favicon.svg`
2. Design at 32x32 `viewBox`
3. Keep shapes bold — thin lines disappear at 16x16
4. Max 2-3 colours
5. Test at 16px by reading the SVG back
6. For HTML: `<link rel="icon" href="/favicon.svg" type="image/svg+xml">`
7. For PNG fallback: `sharp -i favicon.svg -o favicon.png --resize 32`

## OG Image Generation

1. Start from `library/templates/og-image.svg`
2. 1200x630 viewBox (standard Open Graph)
3. Large text (40px+ for title), bold colours
4. Must read at 600x315 (half size in feeds)
5. Convert to PNG: `sharp -i og.svg -o og.png --resize 1200`
6. For HTML: `<meta property="og:image" content="/og.png">`

---

## svgo Optimisation

### Check availability
```bash
which svgo  # or: npx svgo --version
```

### Common commands
```bash
# Optimise single file
svgo input.svg -o output.svg

# Optimise folder
svgo -f ./icons/ -o ./icons-optimised/

# Pretty output (readable)
svgo input.svg -o output.svg --pretty

# Remove dimensions (keep viewBox only)
svgo input.svg -o output.svg --config='{"plugins":[{"name":"removeDimensions"}]}'
```

### Manual optimisation checklist (if svgo unavailable)
- [ ] Remove editor metadata (`inkscape:`, `sodipodi:`, `data-name`)
- [ ] Remove empty `<g>` groups and unused `<defs>`
- [ ] Collapse nested `<g>` groups where possible
- [ ] Round coordinates to 1-2 decimal places
- [ ] Use shorthand path commands (relative, `H`/`V` instead of `L`)
- [ ] Convert `<style>` to attributes if it's smaller
- [ ] Remove default attribute values (`fill="#000"` is default)
- [ ] Merge overlapping identical shapes
- [ ] Use `<use>` for repeated elements
- [ ] Set `viewBox`, remove fixed `width`/`height`
- [ ] Add `xmlns` only on the root `<svg>`

---

## Accessibility

- Always add `role="img"` to decorative/informational SVGs
- Always add `<title>` as first child with a descriptive label
- Add `<desc>` for complex illustrations
- For inline SVGs, use `aria-labelledby="titleId"` pointing to the `<title>`
- For decorative-only SVGs, use `aria-hidden="true"` and omit title
- Ensure sufficient colour contrast (4.5:1 for text, 3:1 for graphics)
- Don't rely on colour alone to convey meaning

---

## Animation Patterns

### CSS (preferred for simple animations)
```xml
<style>
  @keyframes spin { to { transform: rotate(360deg); } }
  .spinner { animation: spin 1s linear infinite; transform-origin: center; }
</style>
```

### SMIL (for path morphing)
```xml
<path d="M10 80 Q 95 10 180 80">
  <animate attributeName="d" dur="1s" repeatCount="indefinite"
    values="M10 80 Q 95 10 180 80; M10 80 Q 95 150 180 80; M10 80 Q 95 10 180 80"/>
</path>
```

### Performance tips
- Animate `transform` and `opacity` (GPU-accelerated)
- Avoid animating `d`, `filter`, or layout properties in loops
- Use `will-change: transform` on animated elements
- Keep animations under 60fps — test on low-end devices

### Ready-made animations in library
- `library/animations/spinner.svg` — loading spinner (track + rotating head)
- `library/animations/pulse.svg` — notification pulse with expanding ring
- `library/animations/fade-in-up.svg` — entrance animation with stagger support
