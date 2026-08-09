---
name: layered-elevation-shadows
description: Use for refined, neutral surface elevation on cards, panels, popovers, or hero media — several stacked box-shadow layers instead of Tailwind's default single-shadow scale, which tends to read as blunt or generic at this level of polish.
---

# Layered Elevation Shadows

## Why one shadow reads as generic
A single `box-shadow` models one light source at one distance, so it always looks slightly artificial — real ambient shadow is the sum of many light contributions at different distances, which is soft near the surface and diffuse further out. Stacking several shadow layers, each with a different offset/blur/spread combination and a very low opacity, approximates that instead of faking it with one heavy shadow. The result reads as depth rather than "a dark blob under a box."

## Three elevation tiers
**Small** — compact cards, form controls, pills, quieter surfaces. Notice the third layer is a `0 0 0 1px` shadow at very low alpha — that's a hairline border baked into the shadow itself, not a separate `border` rule:
```
shadow-[0px_2px_3px_-1px_rgba(0,0,0,0.1),0px_1px_0px_0px_rgba(25,28,33,0.02),0px_0px_0px_1px_rgba(25,28,33,0.08)]
```

**Medium** — the default elevated surface: cards, panels, popovers. Six layers stepping up in blur/offset together, each barely-there (6% alpha) on its own, additive in effect:
```
shadow-[0px_0px_0px_1px_rgba(0,0,0,0.06),0px_1px_1px_-0.5px_rgba(0,0,0,0.06),0px_3px_3px_-1.5px_rgba(0,0,0,0.06),0px_6px_6px_-3px_rgba(0,0,0,0.06),0px_12px_12px_-6px_rgba(0,0,0,0.06),0px_24px_24px_-12px_rgba(0,0,0,0.06)]
```

**Large** — hero media, feature callouts, modal-like containers; the strongest lift, for the one or two elements per screen that should feel like they're floating above everything else:
```
shadow-[0_2.8px_2.2px_rgba(0,0,0,0.034),0_6.7px_5.3px_rgba(0,0,0,0.048),0_12.5px_10px_rgba(0,0,0,0.06),0_22.3px_17.9px_rgba(0,0,0,0.072),0_41.8px_33.4px_rgba(0,0,0,0.086),0_100px_80px_rgba(0,0,0,0.12)]
```

## Applying one
```html
<div class="rounded-xl bg-white shadow-[0px_0px_0px_1px_rgba(0,0,0,0.06),0px_1px_1px_-0.5px_rgba(0,0,0,0.06),0px_3px_3px_-1.5px_rgba(0,0,0,0.06),0px_6px_6px_-3px_rgba(0,0,0,0.06),0px_12px_12px_-6px_rgba(0,0,0,0.06),0px_24px_24px_-12px_rgba(0,0,0,0.06)]">
  ...
</div>
```

## How to pick between the three
- Small for anything compact or dense — form controls, pills, tight card grids.
- Medium as the default whenever a surface just needs to feel "lifted" — most cards and panels land here.
- Large only for the handful of elements per screen that should feel like they're floating well above the page — reserve it, don't spread it around.

## Rules that keep this looking intentional
- Pick one elevation tier per component and stick to it across states, unless an interaction (hover, active, drag) is a deliberate reason to change elevation.
- Never mix one of these stacks with a plain default shadow scale on the same element — the two read as visually inconsistent depth systems fighting each other.
- Keep the shadow neutral. Tinting it with a strong color turns "elevation" into "glow," a different effect entirely.
- Don't apply the large tier to dense lists or small controls — it overwhelms anything that isn't meant to be a hero element.
- Don't stack more than one of these shadow utilities on a single element; each already contains everything it needs.
- These aren't a substitute for an actual border in a genuinely low-contrast layout — if a surface needs a hard edge to read against its background, add one explicitly rather than hoping the shadow does that job too.
