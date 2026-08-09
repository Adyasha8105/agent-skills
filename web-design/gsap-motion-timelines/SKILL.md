---
name: gsap-motion-timelines
description: Use when adding or debugging orchestrated web animation with GSAP — entrance sequences, scroll-driven sections, staggered reveals. Covers timeline composition, ScrollTrigger setup, performance traps, and SPA cleanup.
---

# GSAP Motion Timelines

## Where this earns its keep
CSS transitions handle one element reacting to one state change. The moment motion needs to be sequenced — five elements entering in order, a scroll position driving multiple properties at once — a timeline-based library removes the need to hand-calculate delays.

## The three building blocks
- A single tween: `gsap.to(target, vars)` animates toward the given values; `gsap.from(target, vars)` animates *from* them back to the element's current state — useful for "reveal" entrances where the resting state is already correct in CSS.
- A timeline strings tweens together with control over overlap:
  ```js
  const tl = gsap.timeline({ defaults: { ease: "power2.out", duration: 0.6 } })
  tl.from(".nav", { y: -20, autoAlpha: 0 })
    .from(".hero-title", { y: 30, autoAlpha: 0 }, "-=0.2")   // starts 0.2s before the previous ends
    .from(".hero-cta", { scale: 0.95, autoAlpha: 0 }, "-=0.2")
  ```
  The position argument (`"-=0.2"`, `"+=0.5"`, a label, or an absolute time) is what makes overlap explicit instead of guessed.
- Stagger spreads one tween across a set of targets: `stagger: { each: 0.05, from: "center" }` fans out from the middle instead of left-to-right — useful for grids.

## Scroll-driven sections (ScrollTrigger)
```js
gsap.registerPlugin(ScrollTrigger)

gsap.timeline({
  scrollTrigger: {
    trigger: ".story",
    start: "top top",
    end: "+=800",
    scrub: 1,
    pin: true,
  },
}).to(".story .panel", { xPercent: -200 })
```
`scrub: 1` ties timeline progress directly to scroll position (no easing/momentum); `pin: true` locks the trigger element in place while the timeline plays out underneath it.

## Where this breaks in practice
- **Animating layout properties** (`top`, `left`, `width`, `height`) forces the browser to recompute layout on every frame. Animate `x`/`y`/`scale`/`rotation` and `autoAlpha` (opacity + visibility combined) instead — these are compositor-only and don't trigger reflow.
- **ScrollTrigger silently not firing** is almost always a sizing problem: the trigger element has no height, or scroll is happening inside a nested container ScrollTrigger doesn't know about. Confirm the trigger has real dimensions before debugging the animation itself.
- **Measuring before the page has settled** — fonts and images loading after ScrollTrigger positions are calculated will throw start/end points off. Call `ScrollTrigger.refresh()` once images/fonts have loaded, not just on initial mount.
- **Leaking animations in an SPA.** On unmount, either wrap setup in `gsap.context()` and call `.revert()`, or explicitly kill every trigger: `ScrollTrigger.getAll().forEach(t => t.kill())`. Otherwise triggers from an unmounted route keep listening and firing.
- **Ignoring reduced-motion preference.** Wrap non-essential animation setup in a check against `window.matchMedia("(prefers-reduced-motion: reduce)").matches` and skip straight to the end state for users who've asked for it.

## Before wiring this up, check
- Static site or SPA (React/Vue/Svelte)? SPA changes how and when cleanup needs to run.
- Does anything need to be pinned, scrubbed, or snapped to scroll position, or is this all entrance/exit motion?
- Any constraint on mobile performance, or an explicit reduced-motion requirement?
