---
name: webgl-hero-direction
description: Use when a landing page's hero needs a WebGL-driven visual and the choice of *what kind* isn't obvious yet — steering a brand goal (premium, technical, playful, cinematic) into one deliberate visual direction, instead of defaulting to particles or a spinning object. Covers picking a lane, sizing the implementation risk, and the performance/accessibility gates a WebGL hero needs before shipping.
---

# WebGL Hero Direction

A WebGL hero is a design decision before it's an implementation one. Picking the *category* of effect first, then building, avoids the common failure mode of writing a shader and only afterward asking whether it fits the brand.

## Name the goal before touching code
Two things decide the direction, not aesthetics alone:
- **The signal the brand wants to send** — premium/confident, technical/credible, playful/social, or cinematic/campaign-scale.
- **The constraints already in place** — device mix (desktop-heavy vs. mostly mobile), a stated reduced-motion policy, and how much production time and maintenance budget actually exists for this.

## Four directions, pick one
Mixing three or four heavy effects into a single hero is the fastest way to make a page feel unfocused. Choose the lane that matches the goal above, and let everything else stay quiet.

- **Ambient depth** — soft gradient meshes, slow parallax, light bloom. For SaaS/B2B pages where legibility has to win; the effect stays secondary to the copy at all times.
- **Systems in motion** — particle flows, node networks, wireframes that suggest "a system is running," not decoration for its own sake. Fits AI/infra/developer products where technical credibility is the goal.
- **One hero object** — a single centered 3D model (product, device, abstract sculpture) with a controlled camera move or interaction. Fits launches and product pages where there's a real object to show.
- **Full cinematic scene** — a choreographed, scroll-driven sequence with heavier composition and postprocessing. Reserve this for campaign microsites where spectacle *is* the KPI, not a default page treatment.

## Match the lane to the page type
A waitlist or pricing page should stay in ambient-depth territory — the offer needs to read in seconds, and WebGL should never stand between a visitor and understanding it. A product feature page earns the one-hero-object treatment when there's an actual product to show. Enterprise trust pages can use systems-in-motion, but with a restrained palette. Campaign microsites are the only place the full cinematic lane belongs by default, and even then only with an explicit lower-end fallback.

## Ship it through four gates, not one
1. **Message gate** — the headline and primary action must be understandable in under three seconds; the WebGL layer never gets to block that.
2. **Performance gate** — cap `devicePixelRatio` around 1.5–2, show an immediate non-WebGL poster while the scene initializes, and lazy-load anything heavy.
3. **Accessibility gate** — honor `prefers-reduced-motion` with a still frame or low-motion fallback, and keep text contrast legible over an animated background at every frame, not just the first one.
4. **Reliability gate** — handle WebGL context loss and window resize, and dispose geometries/materials/textures on route change so nothing leaks in a single-page app.

## Size the implementation risk honestly
- **Low risk** (fastest to ship): CSS plus a canvas illusion, or one or two lightweight shaders.
- **Medium risk**: a real 3D scene with a handful of meshes and light postprocessing.
- **High risk**: multi-pass shaders, dense particle fields, heavy postprocessing stacks.

Default to low or medium unless the brief explicitly calls for campaign-grade immersion — that's a deliberate scope decision, not something to back into mid-build.

## Common failures and their actual fix
- *"Looks cool but conversion dropped"* — the effect is competing with the CTA. Reduce motion amplitude, darken or soften the background, raise CTA contrast.
- *"Mobile stutters"* — cut the particle count and lower the DPR cap before touching anything else; drop expensive postprocessing next.
- *"Feels generic"* — pick one signature motif tied to the brand (a grid, a wave, an orbit) instead of a library default.
- *"The team can't maintain the shader"* — simplify to a modular scene with named, documented parameters rather than inline magic numbers.

## What to hand back
The chosen lane and why, a short visual spec (palette, motion behavior, composition), the implementation risk tier, the fallback behavior, and confirmation the four gates above are met, before layering on more visual complexity.
