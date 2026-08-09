---
name: cobe-lightweight-globe
description: Use for a small, decorative-or-interactive spinning globe with location markers — cobe is a purpose-built, lightweight alternative to pulling in full Three.js when a globe is the only 3D element needed. Covers setup, the per-frame rotation pattern, DPR canvas sizing, and resize handling.
---

# Cobe Lightweight Globe

## Why reach for this over Three.js
If a spinning globe with a few location markers is the *only* 3D thing on the page — a hero decoration, an "our users are everywhere" section — pulling in Three.js is a lot of weight for one effect. Cobe is built for exactly this one use case and stays small.

## The core pattern: a render callback, not your own loop
```js
import createGlobe from "cobe"

const globe = createGlobe(canvas, {
  ...options,
  onRender(state) {
    // mutate `state` here every frame; cobe owns the actual render loop
  },
})
```
The library drives its own internal animation frame loop and calls `onRender` on every tick, handing over a mutable `state` object. Continuous rotation comes from mutating `state.phi` inside that callback — there's no separate `requestAnimationFrame` to manage on the calling side; cobe already owns that.

## Canvas sizing has to account for device pixel ratio manually
```js
const rect = canvas.getBoundingClientRect()
const dpr = Math.min(window.devicePixelRatio, 2)   // clamp — an uncapped DPR on a high-density phone is wasted GPU work
canvas.width = Math.round(rect.width * dpr)
canvas.height = Math.round(rect.height * dpr)
```
The CSS size (`getBoundingClientRect`) controls how large the canvas *displays*; the `canvas.width`/`height` attributes control how many actual pixels get rendered into it. Setting only one of these either blurs the globe (CSS size bigger than the pixel buffer) or wastes GPU cycles rendering pixels that get scaled back down (pixel buffer bigger than needed). Both have to move together, scaled by the same `dpr`.

## Full setup with rotation and a marker
```js
import createGlobe from "cobe"

const canvas = document.getElementById("cobe")
let phi = 0

function setup() {
  const rect = canvas.getBoundingClientRect()
  const dpr = Math.min(window.devicePixelRatio, 2)
  canvas.width = Math.round(rect.width * dpr)
  canvas.height = Math.round(rect.height * dpr)

  const globe = createGlobe(canvas, {
    devicePixelRatio: dpr,
    width: canvas.width,
    height: canvas.height,
    phi: 0,
    theta: 0.2,
    dark: 0,
    diffuse: 1.2,
    scale: 1,
    mapSamples: 16000,
    mapBrightness: 6,
    baseColor: [0.2, 0.2, 0.25],
    glowColor: [1, 1, 1],
    markerColor: [0.8, 0.5, 1],
    markers: [{ location: [1.3521, 103.8198], size: 0.08 }],   // [lat, lon]
    onRender: (state) => {
      state.phi = phi
      phi += 0.01
    },
  })

  return globe
}

let globe = setup()
window.addEventListener("resize", () => {
  globe.destroy()   // cobe doesn't support resizing an existing instance's internal buffers
  globe = setup()    // so a resize means tear down and rebuild, not patch in place
})
```

## Lifecycle methods
- `globe.toggle()` — pauses/resumes the internal render loop, useful for pausing when the globe scrolls offscreen (pair with an `IntersectionObserver`, same pattern used for any other continuously-animating canvas).
- `globe.destroy()` — tears the instance down fully; required before creating a replacement on resize, and required on component unmount in a framework to avoid a leaked render loop.

## Where this goes wrong
- **CSS size and canvas pixel size set independently** (or only one of them updated) — produces either a blurry globe or one silently rendering more pixels than will ever be visible.
- **Not recreating on resize** — the globe keeps rendering at its original pixel dimensions regardless of how its container has changed size.
- **Uncapped device pixel ratio on mobile** — a phone with a DPR of 3 without the `Math.min(..., 2)` clamp roughly doubles the pixel-fill cost for a difference that's barely perceptible at globe scale.

## Before building, confirm
- Placement and size — hero, section, or a small card — since that drives the DPR/performance tradeoff.
- Marker locations and colors, matched to brand rather than left at defaults.
- Ambient auto-spin, or pointer-drag-to-rotate interaction instead?
