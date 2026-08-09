---
name: progress-line-scroll-story
description: Use to turn any ordered process (onboarding, a recipe's steps, a roadmap, a checkout flow, a case study) into a scroll-driven timeline with a line that fills as the reader progresses and steps that light up as they're reached. Covers layout choice, accessibility, and reduced-motion behavior.
---

# Progress-Line Scroll Story

The rule that matters most: the sequence has to be complete, readable, and navigable *before* any animation is added. The line, the dots, the active-state glow — all of that enhances a real ordered list; none of it is allowed to be the only thing carrying the structure.

## Model it as data, not markup
```js
const steps = [
  { id: "brief", number: "01", title: "Set the direction", body: "..." },
  { id: "build", number: "02", title: "Make the system",   body: "..." },
  { id: "ship",  number: "03", title: "Release and learn", body: "..." },
]
```
Render this as a real ordered list with real headings first. Everything visual gets layered on top of that — never generated in a way that only exists as decoration with no underlying semantic structure.

## Building the line itself
1. A quiet base line sits behind every point, always fully drawn — it's the "track."
2. A second line, the progress fill, sits on top of it with `transform-origin: top` (vertical) or `left` (horizontal) — so it draws by scaling from a fixed origin rather than by animating length or position directly.
3. Measure from the *center of the first point* to the *center of the last point* — not the edges of whatever sections happen to contain them. Section padding varies; point centers don't.
4. Normalize the current scroll position against those two centers into a `0–1` progress value.
5. Apply that as `scaleY(progress)` / `scaleX(progress)` — a transform, so the update stays on the compositor instead of forcing layout on every scroll tick.
6. A step becomes "active" the moment the progress head's position crosses that step's center point — not on some independent visibility threshold that can disagree with where the line actually is.

```js
const progress = Math.min(1, Math.max(0, (viewportAnchor - lineStart) / (lineEnd - lineStart)))
line.style.transform = `scaleY(${progress})`
```

Batch every DOM write inside a single animation frame, and recompute the line's geometry after fonts/images finish loading, on resize, on orientation change, and after any content mutation — anything that can shift where the point centers actually sit needs a remeasure, or the line and the active state will drift apart from each other.

## Picking a layout
- **Centered, alternating sides** — only works when both sides have comparable width and similar content weight; lopsided steps look broken here.
- **Left rail** — the safer default for long copy, compact steps, or cards with uneven heights.
- **Horizontal line** — for short sequences with concise labels; needs explicit, keyboard-reachable overflow handling since horizontal scroll is easy to get trapped in.
- **Pinned full-screen chapters** — only when each step genuinely carries a distinct visual state worth dedicating a full viewport to. Keep every pin finite and release it cleanly before the next section starts.
- On small screens, collapse straight to a left rail. Don't preserve side-alternation at the cost of natural reading order — that's a desktop affordance, not a requirement.

## Animating the step states
Small, restrained transitions only — opacity, a slight translate or scale, blur, a color shift, a media crossfade. Every step needs to stay legible even while inactive; "inactive" isn't the same as "unreadable." Use `aria-current="step"` on the active step when that state is meaningful, but don't wire up a live region that announces every scroll tick — that's noise, not information.

A plain IntersectionObserver is enough for simple "this step entered view" logic. Reach for a normalized scroll measurement (or ScrollTrigger, if already using GSAP) only when the line itself needs to fill continuously or when pinned media needs to stay coordinated with it.

## Navigation has to keep working
- If a step can be jumped to directly, it needs a real anchor link — not a decorative dot pretending to be a control.
- Account for `scroll-margin` if there's a sticky header, or an anchor jump lands the target behind it.
- Passive scrolling should never steal or move keyboard focus.
- Don't rewrite the URL or push history entries on passive scroll — only when a user explicitly clicks a step.
- Any interactive step needs a real button or link with a visible focus state.

## Reduced motion
Under `prefers-reduced-motion: reduce`: show either the fully-drawn line or discrete "reached" states, with no scrubbed interpolation, no blur, no pinning, and no large transforms. The document should still read in normal flow with every step's content present — reduced motion removes the animation, not the content.

## Before shipping, check
- A variable number of steps, and uneven card heights between them.
- A step with missing media, and a step with unusually long text (translation-length text is a good stress test).
- Common breakpoints (390 / 768 / 1024 / 1440) and 200% browser zoom.
- Fast scrolling in both directions — the active step and the line head should never disagree about where the reader is.
- Direct anchor navigation, keyboard-only navigation order, and reduced-motion mode all independently.
- Late-loading fonts or images that shift layout after the initial geometry measurement.
- Cleanup on route change in an SPA, and a clean console with no leftover errors.
