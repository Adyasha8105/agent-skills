---
name: gradient-border-edge
description: Use for a refined gradient edge highlight on cards, pricing panels, nav bars, modals, or hero surfaces — a subtler alternative to a flat solid border or a loud glow. Two pure-CSS techniques depending on whether the surface's background needs to stay untouched.
---

# Gradient Border Edge

## Why not just `border: 1px solid`
A flat border reads as a boundary; a quiet gradient border reads as an edge catching light — the difference between "this is a box" and "this is a surface." The effect only works subtly: most gradient stops should sit under ~40% opacity, and the border needs to stay quieter than whatever it's framing.

## Defaults to start from
- Width: `1px` normally, `2px` only for a large hero card or an active/selected state.
- Radius: always inherited from the parent surface — a mismatched radius is the most common way this breaks visually.
- Angle: `135°`–`160°` reads as light falling naturally from upper-left; steeper or shallower angles start looking arbitrary.
- Stops: a neutral highlight, one brand-accent color, then a fade back to neutral — resist the urge to add a second accent color.

## Technique 1: two backgrounds, two box origins
Use this when the surface's own fill is a plain solid or translucent color. The trick is layering two gradients on the same `background` property, each anchored to a different box:
```css
.gradient-edge {
  --surface: rgba(10, 14, 24, 0.72);
  --edge-a: rgba(255, 255, 255, 0.34);
  --edge-b: rgba(125, 92, 255, 0.36);
  --edge-c: rgba(255, 255, 255, 0.08);

  border: 1px solid transparent;
  border-radius: 20px;
  background:
    linear-gradient(var(--surface), var(--surface)) padding-box,
    linear-gradient(135deg, var(--edge-a), var(--edge-b), var(--edge-c)) border-box;
}
```
The first gradient is anchored `padding-box` — it fills everything *inside* the transparent border, i.e. the surface's actual content area. The second is anchored `border-box` — it fills the full box including the border ring, but since the first layer already covers the inner area, only the ring itself shows the second gradient. The `border: 1px solid transparent` isn't decorative — it's what reserves the ring's width for the second layer to fill.

## Technique 2: a masked pseudo-element, for surfaces you can't touch
Use this when the surface already has its own complex background (an image, an existing gradient, a blur) that the border effect must not overwrite:
```css
.gradient-edge-mask {
  position: relative;
  border-radius: 20px;
}

.gradient-edge-mask::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: linear-gradient(145deg,
    rgba(255, 255, 255, 0.34),
    rgba(125, 92, 255, 0.36) 45%,
    rgba(255, 255, 255, 0.08));
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  pointer-events: none;
}
```
Here the gradient lives entirely on a `::before` overlay, so the real surface underneath is never touched. The mask is the interesting part: it draws two identical white shapes — one clipped to `content-box` (the pseudo-element's box minus its `padding`), one at full size — then subtracts one from the other (`exclude`/`xor`). What's left visible is only the thin ring defined by that `padding: 1px`, which is exactly where the gradient should show. Everything inside that ring is masked away, letting the real content underneath show through untouched.

## A Tailwind arbitrary-property version, for one-off cases
```html
<div class="rounded-2xl border border-transparent
  [background:linear-gradient(rgba(10,14,24,.72),rgba(10,14,24,.72))_padding-box,linear-gradient(135deg,rgba(255,255,255,.34),rgba(125,92,255,.36),rgba(255,255,255,.08))_border-box]">
  ...
</div>
```
Same technique 1 mechanics, just inlined — reach for the real CSS class instead once this shows up on more than one component.

## Taste calls
- Apply this to one hierarchy level at a time — the primary card, the active tab, the selected pricing plan — never to every card on a page simultaneously; that turns a highlight into wallpaper.
- No rainbow borders, no full-saturation neon, no gradient that itself animates, as a default choice.
- The border should frame the content, not compete with it for attention — if it's the first thing noticed, the opacity stops are too high.
- A soft shadow or a faint inner highlight is enough accompanying depth; stacking more effects on top starts to look busy.
- Check light and dark themes independently — the same alpha values essentially never read the same on both.

## Pre-ship checklist
- No accidental double border from an existing `border-color` still set on the element.
- The radius on the border effect matches the underlying surface's radius exactly, not an approximation.
- The gradient reads clearly at normal zoom, but doesn't jump out while quickly scanning the page.
- Focus states remain accessible on their own — never rely on the gradient alone to signal focus.
