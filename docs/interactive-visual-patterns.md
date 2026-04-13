# Interactive Visual Patterns: Service Sketches Reference

**Status:** Approved direction. Brent likes these. Use as the reference pattern for any future interactive visuals on dxn.is or related properties.

**Source:** `~/dev/dxnis/js/service-visuals.js`

---

## Core design principles

These principles govern all three service sketches and should be applied to any new interactive visual on the site.

### 1. Scroll is the only interaction

The scroll position directly controls the visual frame. Scroll at 50% shows the 50% state. Scroll backward and the visual reverses. When scrolling stops, nothing moves. No hover. No click. No time-based animation. The scroll IS the timeline.

This is the Apple homepage pattern: the page tells a story as you scroll through it. Each visual has a beginning state (scroll = 0) and an ending state (scroll = 1) and every frame between is a deterministic function of the scroll position.

Implementation: each sketch receives `progress` (0 to 1) from `window.__serviceScrollState` which is set by the services scroll JS in `main.js`. The sketch's `draw()` function computes ALL visual properties as pure functions of `progress`. No `frameCount`, no timers, no physics simulation.

### 2. Easing via smoothstep

Raw scroll progress is linear which feels mechanical. Apply smoothstep easing (`t * t * (3 - 2 * t)`) to the progress value before using it for visual interpolation. This makes the start and end of the animation feel natural without adding complexity.

### 3. Theme-aware colors via dynamic functions

Colors are never hardcoded. They come from functions that check the current theme:

```javascript
function gold() { return isDarkMode() ? [255, 233, 74] : [180, 145, 10]; }
function coral() { return isDarkMode() ? [232, 100, 100] : [200, 70, 70]; }
function teal() { return isDarkMode() ? [80, 160, 176] : [40, 110, 130]; }
```

Dark mode uses bright saturated values (designed to glow against #09090B). Light mode uses darker, more saturated values (designed to pop against warm cream backgrounds, WCAG AA compliant).

### 4. Lifecycle management

Sketches create and destroy themselves based on visibility. When a service panel scrolls out of view, the p5 instance is removed and the canvas is cleaned up. When it scrolls back in, a fresh instance is created from scratch. Every visit starts from the beginning.

Implementation: `manageVisibility()` wraps each sketch in an IntersectionObserver. On intersect: create. On leave: `instance.remove()` and clear canvases.

### 5. Responsive

All positions and sizes use relative values (`p.min(w, h) * 0.32` for radius, `w * 0.08` for margins). On `windowResized`, canvases resize and computed positions update. On mobile (under 768px), the pinned scroll behavior is disabled and panels stack vertically, so the sketches render at whatever container size they get.

### 6. p5.js instance mode

All sketches use p5.js instance mode (`new p5(function(p) { ... }, container)`) so multiple canvases coexist on the same page without interfering with each other or with the hero particle sketch (which runs in instance mode too).

---

## The three sketches

### Sketch 1: Alignment Network (Strategic Planning & Facilitation)

**Visual metaphor:** Diverse perspectives aligning into a coherent whole.

**Start state (progress = 0):**
- 14 gold nodes scattered randomly across the canvas
- Faint dashed teal connections between nearby nodes (distance threshold = 80px)
- Nodes at 80 alpha, small size (1x)
- No center node visible

**End state (progress = 1):**
- All 14 nodes in a precise circle formation (radius = 32% of min dimension)
- Solid teal connections between all nearby nodes (distance threshold = 260px)
- Connections at 2.2px stroke weight
- Nodes at 255 alpha with 1.6x size and soft glow halos
- Center facilitator node visible at 22px with a glow halo

**Key visual elements:**
- Gold nodes: `p.fill(...gold(), alpha)`. Size = `baseSize * lerp(1, 1.6, t)`. Glow halo at 4x size when `t > 0.4`.
- Teal connections: dashed (`setLineDash([3, 5])`) when `t < 0.4`, solid above. Stroke weight interpolates 0.4 to 2.2.
- Connection distance threshold: `lerp(80, 260, t)`. At progress 0, only very close nodes connect. At 1, the whole network is visible.
- Center facilitator: appears at `t > 0.1`, grows from 0 to 22px. Has a 3x glow halo.

**Node layout:**
- Start: `random(8%-92% of width, 8%-92% of height)` per node, computed once in setup
- End: evenly spaced around a circle at center, `angle = (TWO_PI / 14) * i`

**Brand color assignments:** Gold for nodes. Teal for connections. No coral.

---

### Sketch 2: Radial Pulse (Workshops & Speaking)

**Visual metaphor:** A catalyst at the center energizing concentric rings of participants.

**Start state (progress = 0):**
- Coral center catalyst at 10px, dim (120 alpha)
- All participant nodes invisible (at center, collapsed)
- No connections

**End state (progress = 1):**
- Center catalyst at 20px, bright (240 alpha), with 2.5x glow halo
- Inner ring (6 gold nodes) at full radius (15% of min dimension), full brightness
- Outer ring (10 gold nodes) at full radius (32% of min dimension), full brightness
- Connections between nearby nodes visible at 100 alpha
- Expanding pulse ring at full extent

**Staged reveal (not all at once):**
- Progress 0 to 0.4: inner ring nodes expand from center to their positions, fade in
- Progress 0.2 to 0.7: outer ring nodes expand from center to their positions, fade in
- Progress 0.5 to 1.0: connections between nearby nodes fade in
- Progress 0 to 1: pulse ring continuously expands (radius = `lerp(10, 40% of min dim, prog)`)

**Key visual elements:**
- Center catalyst: coral filled circle with coral glow halo. Size interpolates with progress.
- Participant nodes: gold filled circles. Each ring has its own progress ramp (inner leads, outer follows). Nodes expand FROM center (radius multiplied by their ring's progress). Glow halos appear at 0.5+ progress per ring.
- Ring guides: coral ellipses at 0.5px stroke, very low alpha (12), fade in with their ring's progress.
- Pulse ring: coral stroke at 2px + 6px glow layer. Radius is a continuous function of overall progress. Alpha decreases as it expands.

**Node layout:**
- Inner ring: 6 nodes evenly spaced at 15% radius, slight random angle jitter
- Outer ring: 10 nodes evenly spaced at 32% radius, slight random angle jitter

**Brand color assignments:** Coral for center and ring guides. Gold for participant nodes and connections.

---

### Sketch 3: Gravitational Pair (Coaching & Advisory)

**Visual metaphor:** Two entities in relationship, drawn closer through the coaching engagement.

**Start state (progress = 0):**
- Two teal nodes in a wide orbit (radius = 28% of min dimension)
- Dim nodes (120 alpha, 12px)
- Faint connection line (30 alpha, 0.5px)
- Very faint orbit path hint (5 alpha)
- Faint trail

**End state (progress = 1):**
- Tight orbit (radius = 8% of min dimension)
- Bright nodes (255 alpha, 22px) with glow halos (55px)
- Strong connection line (200 alpha, 2.8px)
- Visible orbit path (20 alpha)
- Bright trail

**Orbit mechanics:**
- Angle = `progress * TWO_PI * 3` (three full rotations across the scroll range)
- Both nodes are always diametrically opposed (`angle` and `angle + PI`)
- Orbit radius = `lerp(wide, tight, progress)`
- When scrolling stops, angle stops. When scrolling backward, orbit reverses.

**Key visual elements:**
- Two teal nodes: larger node (1x size) and smaller node (0.85x size). Glow halos at corresponding ratios.
- Connection line: teal stroke connecting both nodes. Alpha and weight interpolate with progress.
- Midpoint marker: teal filled circle at the geometric center of the two nodes. Size inversely proportional to distance (closer = bigger midpoint energy).
- Orbit path hint: teal stroked ellipse at the current orbit diameter. Low alpha, scales with progress.
- Trail: array of recent node positions (60 frames). Each trail line connects the two nodes' historical positions. Visibility scales with progress.

**Brand color assignments:** Teal for everything. Single-color palette reinforces the intimacy of the one-on-one relationship.

---

## How to reuse these patterns elsewhere on the site

### For a new scroll-driven visual:

1. Define start state (progress = 0) and end state (progress = 1)
2. Every visual property = a function of progress (use `lerp`, `map`, `constrain`)
3. Apply smoothstep easing to raw progress
4. Use the theme-aware color functions
5. Wrap in `manageVisibility()` for lifecycle management
6. Use p5.js instance mode
7. Test by scrubbing the scroll slowly and fast in both directions

### For extending the service sketches:

The sketches live in `~/dev/dxnis/js/service-visuals.js`. Scroll state comes from `window.__serviceScrollState` set by `~/dev/dxnis/js/main.js`. The CSS layout is a pinned viewport with `position: sticky` inside a tall scroll track (currently 550vh for 3 panels). Each panel is a `grid-template-columns: 4px 1fr 1fr` where the 4px is a progress stroke that fills with the brand color.

### Visual vocabulary to maintain across all interactive elements:

- Nodes: filled circles with optional glow halos (same circle at 3-4x size, low alpha)
- Connections: teal stroked lines, dashed when weak/uncertain, solid when strong/aligned
- Centers/catalysts: coral, larger, with breathing or pulsing quality
- Participants/elements: gold, smaller, multiple
- Backgrounds: the grainy gradient bloom (radial gradients + SVG noise filter)
- Progress indicators: thin vertical strokes in the brand color, filling top-to-bottom
- Easing: smoothstep for position, linear for alpha
- Dark mode: bright saturated on near-black
- Light mode: rich saturated on warm cream, multiply blend for grain
