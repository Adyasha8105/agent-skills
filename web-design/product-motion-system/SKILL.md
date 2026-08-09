---
name: product-motion-system
description: Use when designing or implementing polished, restrained product motion — the register used by Stripe, Linear, Apple, and Vercel — rather than one-off effects. Covers why to animate at all, reusable motion primitives, duration/easing defaults, choreography patterns, performance rules, and reduced-motion handling.
---

# Product Motion System

The goal isn't more animation, it's animation that earns its place. Every motion in a polished product interface is doing one of five jobs; if it isn't, cut it.

## What motion is actually for
1. **Explaining hierarchy** — showing what matters most right now.
2. **Confirming an action** — feedback that something happened.
3. **Guiding attention** — pointing at where to look next.
4. **Preserving continuity** — keeping spatial relationships intact across a change.
5. **Signaling craft** — the polish layer, used sparingly.

## The shared traits of this register
- **Restraint.** One strong hero moment, everything else is supporting motion — not ten equally-loud animations competing for attention.
- **Deliberate order.** The primary element moves first; secondary elements follow with a small stagger. Motion should read as a sequence, not a simultaneous burst.
- **Human easing, not cartoon easing.** Soft acceleration into a gentle settle. Bounce/elastic easing reads as playful — reach for it only when the product's brand actually is playful, not as a default.
- **Depth used sparingly.** Small parallax, soft shadows, a blur-fade — texture, not spectacle. Heavy 3D only when it's the actual hero moment, not a background flourish.

## Four primitives to build once, reuse everywhere
- **Fade + rise** (default entrance for text blocks, cards, modals): opacity `0 → 1`, `translateY` `12–24px → 0`, duration scaling with size (roughly 300–700ms).
- **Scale + fade** (micro emphasis — popovers, toasts, selected states): scale `0.98 → 1` alongside the opacity fade. Barely-there scale reads as "settling into place," not "growing."
- **Slide** (drawers, step transitions): always via `transform: translate`, never by animating layout properties directly.
- **Morph / shared element** (tab indicators, expanding cards — the highest-craft primitive): requires consistent geometry between the before and after state and a real measured layout, not an approximation.

## Starting numbers
**Durations** — micro hover/press: 120–200ms · toggle/select state change: 180–260ms · popover/toast: 220–320ms · section entrance: 400–800ms · a full hero sequence with internal beats: 800–1600ms.

**Easing** — keep to a small reused set rather than picking per-animation: an `ease-out`-family curve for anything entering, a faster `ease-in`-family curve for anything exiting (things should leave quicker than they arrived). Skip elastic/bounce curves entirely unless the brand voice is explicitly playful.

**Stagger** — 40–90ms per element for text lines or card grids; pull it tighter on mobile, where the same absolute delay reads as more sluggish on a smaller, closer screen.

## Choreography patterns worth reusing
- **Hero → supporting elements**: the hero visual animates first, headline next, call-to-action last — attention flows in the same order the eye should read.
- **Section reveal on scroll**: trigger around 20–30% visibility, and once only — a reveal that replays on every small scroll wiggle reads as broken, not delightful.
- **Hover lift**: a small `-2px` to `-6px` translate up plus a subtle shadow increase; add a border/gradient glow only if the visual direction already uses glow elsewhere.
- **Focus micro-shift**: a focus ring plus a tiny scale/translate on form fields gives tactile confirmation that input was received, beyond just the ring itself.

## Performance is not optional
- Animate `transform` (translate/scale/rotate) and `opacity`. Avoid animating `width`/`height`/`top`/`left` or large-area filters unless there's no alternative — these force layout recalculation on every frame.
- Clamp device pixel ratio to 1–2 on any canvas-heavy effect; an uncapped DPR on a high-density display multiplies pixel-fill cost for no visible benefit.
- Keep blur radii small, and avoid stacking many simultaneously-animated shadows — each one is an extra paint pass.
- Don't measure layout (`getBoundingClientRect`, `offsetWidth`, etc.) inside a scroll or animation loop every frame; batch reads and writes, or use a library that already does.

## Reduced motion is a real requirement, not a nice-to-have
Always honor `prefers-reduced-motion`. The policy: keep all content visible, replace motion with an instant state change plus at most a subtle opacity shift, and disable scroll-scrubbing or pinning entirely — those are the two effects reduced-motion users are most likely to have asked to avoid specifically.

## Choosing an implementation approach
- **Simple sites**: plain CSS transitions cover hovers and toggles fine — no library needed.
- **Product surfaces with several coordinated sequences**: pick one motion library (GSAP or Framer Motion, not both) and define a small token set once — durations, easing curves, standard offsets (8/16/24px), default stagger — instead of hand-picking values per component.
- **Hero moments**: build them as a labeled timeline with distinct beats, and lock any camera/scene movement first before layering text on top of it — text timed against a moving target is fragile to iterate on.

## Before building, establish
- What's the intended motion register — polished-restrained, minimal-functional, cinematic, or developer-tool-precise? (Naming a reference product, e.g. "Stripe-style" or "Linear-style," is a fast way to align on this.)
- What are the actual key moments — a hero sequence, a scroll story, hover cards, nav transitions? Motion effort should concentrate there, not spread evenly.
- Any device/performance constraints (older mobile hardware, low-end GPUs)?
- Is a reduced-motion mode required, or optional-but-expected?

## What to hand back when asked to add this kind of motion
Motion goals (what's being communicated) → motion tokens (durations, easing, offsets) → a choreography plan (the timeline's beats, in order) → implementation notes (performance + reduced-motion handling) → a small working code recipe in whatever stack is in use.
