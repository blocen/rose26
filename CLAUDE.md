# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-page gift card — a canvas animation that progressively reveals a rose photograph for Angela. No build step, no dependencies.

## Running

Open `index.html` directly in a browser, or serve it locally:

```
python3 -m http.server
```

`rose.jpeg` must be in the same directory as `index.html`.

## Architecture

Everything lives in `index.html`:

- **Canvas setup** — fixed 340×510 px (2:3, matching `rose.jpeg`).
- **Animation timeline** — 7 800 ms total, driven by `requestAnimationFrame`. Global progress `t` ∈ [0, 1]. `ph(t, start, end, easing)` maps a sub-range of `t` to [0, 1].
- **Reveal sequence** (ordered):
  1. Stem grows along a cubic Bézier (`BEZ`) as an offset-polygon tube.
  2. Leaves unfurl as scaled ellipses (`LEAVES` array), each with its own `t0`/`t1` window.
  3. Bloom opens petal-by-petal (`PETALS` array of circles), back-to-front.
- **Clipping technique** — all photo regions use `clipDraw(pathFn, alpha)`: begin path → clip → `drawImage`. The image is never painted outside its shape.
- **Post-bloom effects** — radial warm glow, edge vignette, floating petal particles (`ptcls`).
- **End state** (`t >= 1`) — full photo drawn unclipped; message fades in via CSS transition.

## Key constants to adjust

| Constant | Purpose |
|----------|---------|
| `TOTAL` | Animation duration in ms |
| `BEZ` | Four control points of the stem Bézier |
| `LEAVES` / `PETALS` | Per-element geometry (`cx`, `cy`, `rx`/`r`, `rot`) and timing (`t0`, `t1`) |
| `BX`, `BY`, `BR` | Bloom centre and radius |
