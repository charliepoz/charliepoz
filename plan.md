# Cybernetic Illustration Generator — Implementation Plan

A browser-based tool that generates harmonograph-style illustrations inspired by Ivan Moscovich's cybernetic art. Moscovich used a patented double-pendulum harmonograph (1967) to create intricate Lissajous-based figures with multiple colors, varying line weights, and organic decay — described as "a Spirograph on an acid trip."

## Architecture

**Stack:** Vanilla HTML + CSS + JavaScript (single-page app, no build tools, no dependencies)
**Rendering:** HTML5 Canvas (2D context) for real-time drawing
**Output:** PNG export via `canvas.toDataURL()`

## Core Math

The double-pendulum harmonograph equations:

```
x(t) = A₁·sin(f₁·t + p₁)·e^(−d₁·t) + A₂·sin(f₂·t + p₂)·e^(−d₂·t)
y(t) = A₃·sin(f₃·t + p₃)·e^(−d₃·t) + A₄·sin(f₄·t + p₄)·e^(−d₄·t)
```

Parameters per pendulum axis (4 axes total):
- **A** — amplitude (controls size of oscillation)
- **f** — frequency (rational ratios → closed figures; irrational → rotating)
- **p** — phase offset (0° → line, 90° → circle, between → ellipse)
- **d** — damping coefficient (controls spiral-in rate, ~0.0001–0.005)

## Implementation Steps

### Step 1: Project scaffolding
- Create `index.html` with canvas element and control panel sidebar
- Create `style.css` for layout (dark background like Moscovich's prints, sidebar controls)
- Create `harmonograph.js` for the core math and rendering engine

### Step 2: Harmonograph engine (`harmonograph.js`)
- `Harmonograph` class with 4-axis parameters (A, f, p, d for each)
- `computePoint(t)` → returns {x, y} using the parametric equations above
- `draw(ctx, canvas)` method that:
  - Iterates t from 0 to ~100,000 steps (dt ≈ 0.01)
  - Draws line segments between consecutive points
  - Applies color gradient along the path (hue shift over t)
  - Reduces opacity as damping takes effect (mimics pen pressure fading)
  - Uses thin line width (0.5–1.5px) for fine detail

### Step 3: Color system
- Moscovich used multi-colored inks. Implement:
  - HSL-based color cycling along the curve path
  - Configurable palette: rainbow, warm, cool, monochrome, custom
  - Opacity decay tied to damping (lines fade as pendulum slows)
  - Optional: multiple overlaid curves with different base hues

### Step 4: Interactive controls (UI panel)
- **Preset selector** — curated parameter sets that produce aesthetically pleasing figures:
  - "Classic Lissajous" (no damping, simple ratios like 3:2)
  - "Moscovich Spiral" (heavy damping, near-integer frequencies)
  - "Cybernetic Bloom" (multiple overlaid curves)
  - "Decay Study" (high damping, asymmetric)
- **Parameter sliders** for each axis:
  - Frequency (0.1–10, step 0.01)
  - Phase (0–2π)
  - Amplitude (0–1)
  - Damping (0–0.01, step 0.0001)
- **Drawing controls:**
  - Number of iterations / time steps
  - Line width
  - Color palette picker
  - Background color (dark/light toggle)
- **Randomize button** — generates random parameters biased toward aesthetic results
- **Export PNG button**

### Step 5: Multi-layer composition
- Support overlaying 2–4 harmonograph curves on the same canvas
- Each layer gets independent parameters and a distinct base color
- This mimics Moscovich's technique of using multiple colored pens

### Step 6: Animation mode (optional enhancement)
- Animate the drawing process so users see the pen trace the path in real time
- Use `requestAnimationFrame` to progressively reveal the curve
- Play/pause/reset controls

## File Structure

```
charliepoz/
├── index.html          # Main page with canvas + controls
├── style.css           # Dark theme, responsive layout
├── harmonograph.js     # Core engine + rendering
└── readme.md           # Existing file (unchanged)
```

## Key Design Decisions

1. **No build tools** — keeps it simple, forkable, and immediately runnable by opening index.html
2. **Canvas over SVG** — harmonographs produce 100K+ line segments; Canvas handles this efficiently while SVG would choke
3. **Dark background default** — Moscovich's original prints were on dark backgrounds, making the vibrant colored lines pop
4. **Curated presets** — the parameter space is vast and most combinations look bad; presets guide users to the sweet spots
