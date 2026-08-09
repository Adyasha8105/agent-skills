---
name: cursor-spotlight-reveal
description: Use for a cursor-following spotlight that reveals a second, precisely aligned image through a soft radial mask over a base image — before/after, x-ray, material-swap, or colorize-on-hover effects. CSS-mask based, not canvas.
---

# Cursor Spotlight Reveal

## The contract
Two images, identical composition and crop, stacked in the same box. The base image is always fully visible; the second image sits on top of it, clipped by a soft-edged radial mask that follows the pointer. Moving the cursor moves a "window" into the second image over the first.

Do this with a CSS `mask-image` driven by custom properties, not by redrawing a canvas every frame — same visual result, far less allocation, and cleanup is just removing event listeners instead of tearing down a render loop.

## Defaults worth starting from
| Property | Value |
|---|---|
| Spotlight radius (desktop) | `260px` |
| Spotlight radius (compact) | `140–220px` |
| Pointer position easing | `0.1` |
| Radius easing | `0.14` (slightly slower than position — the radius "catching up" reads as intentional, not laggy) |
| Mask falloff | solid through 40%, tapering to fully transparent by 100% |
| On pointer exit | radius eases back to `0`, never snaps |

## Markup
Real `<img>` elements, not background-images — this keeps native loading, intrinsic sizing, and alt text behaving normally.
```html
<figure class="spotlight" data-spotlight data-spotlight-radius="260">
  <img class="spotlight__layer spotlight__layer--base"
       src="/images/base.webp" alt="Description of the visible subject"
       width="1600" height="1000" decoding="async" />
  <img class="spotlight__layer spotlight__layer--reveal"
       src="/images/reveal.webp" alt="" aria-hidden="true"
       width="1600" height="1000" decoding="async" />
</figure>
```
If both images show the same subject in different treatments, the overlay can stay purely decorative (`aria-hidden`). If the reveal carries information the base image doesn't have, that information needs its own accessible path — hover alone can't be how it's discovered.

## The mask
```css
.spotlight {
  --sx: 50%; --sy: 50%; --sr: 0px;
  position: relative;
  overflow: clip;
  isolation: isolate;
  contain: paint;
}

.spotlight__layer {
  display: block;
  width: 100%; height: 100%;
  object-fit: cover;
  object-position: center;
}

.spotlight__layer--base { position: relative; z-index: 0; }

.spotlight__layer--reveal {
  position: absolute;
  inset: 0;
  z-index: 1;
  pointer-events: none;
  -webkit-mask-image: radial-gradient(circle var(--sr) at var(--sx) var(--sy),
    rgb(0 0 0 / 1) 0%, rgb(0 0 0 / 1) 40%, rgb(0 0 0 / 0.75) 60%,
    rgb(0 0 0 / 0.4) 75%, rgb(0 0 0 / 0.12) 88%, transparent 100%);
  mask-image: radial-gradient(circle var(--sr) at var(--sx) var(--sy),
    rgb(0 0 0 / 1) 0%, rgb(0 0 0 / 1) 40%, rgb(0 0 0 / 0.75) 60%,
    rgb(0 0 0 / 0.4) 75%, rgb(0 0 0 / 0.12) 88%, transparent 100%);
  -webkit-mask-repeat: no-repeat;
  mask-repeat: no-repeat;
  will-change: -webkit-mask-image, mask-image;
}

@media (hover: none), (pointer: coarse) {
  .spotlight__layer--reveal { display: none; }
}
```
Both layers must share identical `object-fit`/`object-position` — a one-pixel offset between them is invisible normally but obvious the instant it's inside the spotlight.

## Easing the pointer without wasting frames
```js
function initSpotlight(el) {
  const overlay = el.querySelector(".spotlight__layer--reveal")
  const finePointer = window.matchMedia("(hover: hover) and (pointer: fine)")
  const reduceMotion = window.matchMedia("(prefers-reduced-motion: reduce)")
  if (!overlay || !finePointer.matches) return () => {}

  const s = { x: 0, y: 0, tx: 0, ty: 0, r: 0, tr: 0, inside: false, frame: 0 }

  const radiusFor = () => {
    const requested = Number.parseFloat(el.dataset.spotlightRadius)
    return Number.isFinite(requested) ? requested : Math.min(260, Math.max(140, el.clientWidth * 0.22))
  }

  const setTarget = (clientX, clientY) => {
    const rect = el.getBoundingClientRect()   // local space, not page coords — page coords drift after scroll
    s.tx = clientX - rect.left
    s.ty = clientY - rect.top
  }

  const schedule = () => { if (!s.frame) s.frame = requestAnimationFrame(tick) }

  const tick = () => {
    s.frame = 0
    const posEase = reduceMotion.matches ? 1 : 0.1
    const rEase = reduceMotion.matches ? 1 : 0.14
    s.x += (s.tx - s.x) * posEase
    s.y += (s.ty - s.y) * posEase
    s.r += (s.tr - s.r) * rEase
    el.style.setProperty("--sx", `${s.x.toFixed(2)}px`)
    el.style.setProperty("--sy", `${s.y.toFixed(2)}px`)
    el.style.setProperty("--sr", `${s.r.toFixed(2)}px`)
    const unsettled = Math.abs(s.tx - s.x) > 0.1 || Math.abs(s.ty - s.y) > 0.1 || Math.abs(s.tr - s.r) > 0.1
    if (unsettled) schedule()   // the loop stops itself the moment values converge — no idle ticking
  }

  const onEnter = (e) => {
    s.inside = true
    setTarget(e.clientX, e.clientY)
    if (s.r < 0.5) { s.x = s.tx; s.y = s.ty }   // snap position on first entry so it doesn't sweep in from the center
    s.tr = radiusFor()
    schedule()
  }

  const onMove = (e) => {
    setTarget(e.clientX, e.clientY)
    if (!s.inside) {
      // The page can load with the pointer already resting over this element —
      // in that case the first event is a pointermove with no prior pointerenter.
      s.inside = true
      if (s.r < 0.5) { s.x = s.tx; s.y = s.ty }
      s.tr = radiusFor()
    }
    schedule()
  }

  const hide = () => { s.inside = false; s.tr = 0; schedule() }

  el.addEventListener("pointerenter", onEnter)
  el.addEventListener("pointermove", onMove)
  el.addEventListener("pointerleave", hide)
  el.addEventListener("pointercancel", hide)
  window.addEventListener("blur", hide)

  return () => {
    if (s.frame) cancelAnimationFrame(s.frame)
    el.removeEventListener("pointerenter", onEnter)
    el.removeEventListener("pointermove", onMove)
    el.removeEventListener("pointerleave", hide)
    el.removeEventListener("pointercancel", hide)
    window.removeEventListener("blur", hide)
  }
}

const cleanups = Array.from(document.querySelectorAll("[data-spotlight]")).map(initSpotlight)
```
In a component framework, run this after mount and invoke every returned cleanup function on unmount — never start a fresh animation loop on each render.

## Two edge cases the loop has to handle
1. **Cursor already over the element on page load.** The very first event received can be `pointermove` with no preceding `pointerenter` — the code above checks `!s.inside` inside `onMove` specifically to catch this, otherwise the spotlight would sweep in from the component's center instead of appearing where the cursor already is.
2. **The loop has to know when to stop.** Re-scheduling `requestAnimationFrame` only while position or radius are still more than `0.1px` from their target means the loop goes idle the instant motion settles, rather than ticking forever at 60fps for no visual benefit.

## Optional: a faint parallax grid
If the art direction wants extra atmosphere: a subtle `48px` grid at ~10% opacity, offset by the same eased pointer but recentered around the component's middle, capped to about `±16px` of drift, eased more slowly (`0.06`) than the spotlight itself so it visibly trails rather than tracks 1:1. Disable it entirely under reduced motion. It should read as ambient, not as a second focal point.

## Nested cards that need their own reveal
When a foreground card inside the same scene needs the identical treatment (a glass/refraction card showing the alternate image): reuse the same base/reveal asset pair, read from the same pointer state, but subtract the card's own `getBoundingClientRect()` offset before writing its local mask coordinates — and update the card and the outer scene from the same animation frame so the two don't visibly lag each other.

## Touch and accessibility
- Coarse pointers get the static base image only — don't attempt a touch-drag substitute unless there's a real reason to.
- If the reveal contains information the base image lacks, add an explicit toggle control reachable by touch and keyboard — hover-only discovery excludes both.
- Never gate navigation, pricing, or instructions behind hover-only reveal.
- Under reduced motion, move the mask directly to its target with no trailing ease, and drop any parallax.

## Performance notes
- Drive the mask from CSS custom properties updated inside one shared `requestAnimationFrame` loop — never call `canvas.toDataURL()` per frame, which allocates a new image every tick.
- Stop the loop once values settle; don't let it run indefinitely in the background.
- `will-change` belongs only on the masked overlay layer, not on the whole component.
- Verify both `mask-*` and the `-webkit-mask-*` prefix in Safari — support has historically lagged there.
- Scope the mask to the component's own box; masking a page-sized element when the effect only needs one card wastes compositing work.

## Pre-ship checklist
- Base and reveal images stay pixel-aligned at every breakpoint.
- The spotlight appears under the actual pointer position, not sweeping in from center, on first interaction.
- Loading the page with the cursor already resting over the element still reveals correctly.
- The mask's solid core holds through roughly 40% of the radius, with a smooth (not ringed) falloff to the edge.
- Pointer leave and window blur both collapse the spotlight back to zero.
- Scrolling while hovered doesn't detach the spotlight from the cursor.
- Touch users get a deliberate fallback, not a broken hover state.
- Reduced-motion mode shows no trailing movement and no grid drift.
- The animation loop is confirmed to stop when idle, and cleanup runs on route change in an SPA.
