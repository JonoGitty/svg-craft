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

## Tracing Raster Images — Strategy

When given a PNG/JPG/PDF to recreate as SVG:

1. **Read the image** with the Read tool to see it visually
2. **Identify the major shapes** — background, primary forms, detail elements
3. **Choose abstraction level:**
   - Icon-style: simplify to minimal geometric shapes
   - Illustration-style: preserve character with clean curves
   - Photo-realistic: not suitable for SVG — advise the user
4. **Build bottom-up:** background layer -> main shapes -> details -> highlights
5. **Colour-pick** from the source image — note hex values observed
6. **Iterate** — read back the SVG output to compare visually with the source

### Common patterns
- **Logos**: Convert text to `<path>`, use flat colours, keep under 2 KB
- **Icons**: 24x24 viewBox, 2px stroke, rounded line caps, single colour with optional accent
- **Illustrations**: Group by semantic layer, use gradients sparingly, keep paths simple
- **Decorative backgrounds**: Use `<pattern>` for repeating, `<clipPath>` for shaped sections

## Website Theme Extraction

When fetching a website to match its theme:

1. **Fetch with WebFetch** — get the HTML
2. **Look for CSS custom properties** in `:root` or `html` — these are the design tokens
3. **Look for common class patterns:**
   - `.bg-primary`, `.text-primary`, `.btn-primary` (Tailwind / Bootstrap)
   - `--color-primary`, `--color-secondary`, `--color-accent` (custom properties)
4. **Check meta tags:** `<meta name="theme-color">`
5. **Identify the font stack** — match text style in SVG `<text>` elements
6. **Note border-radius values** — rounded (8px+) vs sharp (0-2px) informs SVG shape style
7. **Check for dark mode** — `prefers-color-scheme` media queries or `.dark` class variants

### Colour palette template
```
Primary:    #______  (brand / headers / CTA)
Secondary:  #______  (supporting elements)
Accent:     #______  (highlights / links)
Background: #______  (page background)
Surface:    #______  (card / panel background)
Text:       #______  (body text)
Muted:      #______  (secondary text / borders)
```

## Accessibility

- Always add `role="img"` to decorative/informational SVGs
- Always add `<title>` as first child with a descriptive label
- Add `<desc>` for complex illustrations
- For inline SVGs, use `aria-labelledby="titleId"` pointing to the `<title>`
- For decorative-only SVGs, use `aria-hidden="true"` and omit title
- Ensure sufficient colour contrast (4.5:1 for text, 3:1 for graphics)
- Don't rely on colour alone to convey meaning

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

## Optimisation Checklist

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
