---
name: layered-progressive-blur
description: Use for a blur that gets stronger right at an edge (top or bottom of the viewport, over scrolling content) and fades smoothly back to no blur a short distance in — the effect behind iOS-style navigation bars and app docks. A single backdrop-filter can't do this; it takes several stacked, individually-masked layers.
---

# Layered Progressive Blur

## Why one `backdrop-filter` isn't enough
`backdrop-filter: blur()` takes a single fixed radius — there's no way to gradient a blur *amount* the way `mask-image` can gradient visibility. So a smooth transition from "heavily blurred" to "not blurred at all" has to be faked: stack several elements, each with its own fixed (and different) blur strength, and mask each one to only be visible within its own narrow band along the axis. Where the bands overlap, the eye blends the discrete steps into what reads as a continuous gradient.

## The formula behind the layers
For `N` layers (8 works well), layer `k` (0-indexed):
- **Blur amount**: `0.5px × 2^k` — doubling each layer (0.5, 1, 2, 4, 8, 16, 32, 64px) covers a huge intensity range in very few steps, since blur perception is roughly logarithmic.
- **Visible band**: spans from `k × (100/N)%` to `(k+3) × (100/N)%` along the fade axis, clipped to `0–100%`. Each band is 3 segments wide (fade-in, solid, fade-out) but only overlaps its *neighbors'* bands, not all of them — that's what keeps this from just looking like uniform maximum blur everywhere.
- The strongest-blur layer sits at the actual edge; each layer moving away from the edge is progressively less blurred, until the last layer fades to fully transparent (no blur at all).

## One worked example (fading from the top edge)
```html
<div class="progressive-blur" data-blur-edge="top">
  <div></div><div></div><div></div><div></div><div></div><div></div>
</div>
```
```css
.progressive-blur {
  position: fixed;
  inset: 0 0 auto 0;      /* anchored to the top; height controls how far the fade travels */
  height: 12%;
  z-index: 5;
  pointer-events: none;   /* this is a visual overlay, not a click target */
}

.progressive-blur > div,
.progressive-blur::before,
.progressive-blur::after {
  position: absolute;
  inset: 0;
}

/* Layer 0 — weakest blur, band [0%, 37.5%] */
.progressive-blur::before {
  content: "";
  backdrop-filter: blur(0.5px);
  mask: linear-gradient(to top, transparent 0%, black 12.5%, black 25%, transparent 37.5%);
}
/* Layer 1 — band [12.5%, 50%] */
.progressive-blur > div:nth-of-type(1) {
  backdrop-filter: blur(1px);
  mask: linear-gradient(to top, transparent 12.5%, black 25%, black 37.5%, transparent 50%);
}
/* Layer 2 — band [25%, 62.5%] */
.progressive-blur > div:nth-of-type(2) {
  backdrop-filter: blur(2px);
  mask: linear-gradient(to top, transparent 25%, black 37.5%, black 50%, transparent 62.5%);
}
/* Layer 3 — band [37.5%, 75%] */
.progressive-blur > div:nth-of-type(3) {
  backdrop-filter: blur(4px);
  mask: linear-gradient(to top, transparent 37.5%, black 50%, black 62.5%, transparent 75%);
}
/* Layer 4 — band [50%, 87.5%] */
.progressive-blur > div:nth-of-type(4) {
  backdrop-filter: blur(8px);
  mask: linear-gradient(to top, transparent 50%, black 62.5%, black 75%, transparent 87.5%);
}
/* Layer 5 — band [62.5%, 100%] */
.progressive-blur > div:nth-of-type(5) {
  backdrop-filter: blur(16px);
  mask: linear-gradient(to top, transparent 62.5%, black 75%, black 87.5%, transparent 100%);
}
/* Layer 6 — band [75%, 100%], truncated (no room left for the far fade-out stop) */
.progressive-blur > div:nth-of-type(6) {
  backdrop-filter: blur(32px);
  mask: linear-gradient(to top, transparent 75%, black 87.5%, black 100%);
}
/* Layer 7 — strongest blur, right at the edge, band [87.5%, 100%] */
.progressive-blur::after {
  content: "";
  backdrop-filter: blur(64px);
  mask: linear-gradient(to top, transparent 87.5%, black 100%);
}
```
Each layer needs its own `z-index` matching its stacking order (0 at the bottom of the stack through 7 at the top) so heavier blur always renders above lighter blur, not the reverse.

## Flipping the edge
To fade from the *bottom* instead: change `inset: 0 0 auto 0` to `inset: auto 0 0 0` (anchor to the bottom), and flip every `linear-gradient(to top, ...)` to `linear-gradient(to bottom, ...)`. The layer/band/blur math is identical — only the anchor and the gradient direction change, which is why this is worth writing as a formula rather than memorizing two separate blocks of near-duplicate CSS.

## Tuning
- **Reach** — the `.progressive-blur` container's `height` (or width, for a horizontal fade) controls how far from the edge the effect travels before fully clearing.
- **Intensity ceiling** — raise or lower the top-end blur value (64px here); everything else in the doubling sequence follows from it.
- **Step count** — fewer layers (e.g. 5–6) is cheaper to render and still reads as smooth in most cases; more layers buys smoother perceived gradation at a real GPU cost.

## Where this fails to do anything
- **No content behind it.** `backdrop-filter` blurs whatever is behind the element — over a flat background color, there's nothing for it to blur, and the whole effect disappears invisibly (not an error, just a no-op).
- **Forgetting `pointer-events: none`.** This overlay sits on top of real content by design; without it, clicks and taps land on the blur layer instead of whatever's underneath.
- **Too many high-blur layers on low-end devices.** Backdrop blur is genuinely GPU-expensive; drop the layer count or cap the maximum blur value for devices where frame rate matters more than gradation smoothness.

## Before building, confirm
- Which edge the blur should emanate from.
- How tall (or wide) the fade zone should be relative to the viewport or container.
- Whether device performance is a real constraint here — that decides the layer count up front.
