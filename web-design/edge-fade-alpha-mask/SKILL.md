---
name: edge-fade-alpha-mask
description: Use for a soft fade at the edge of an element (a marquee, a horizontally scrollable row, a long list, an image bleeding off a container) via a CSS mask-image gradient. Content stays fully rendered underneath — only its visibility fades, not its color.
---

# Edge Fade via Alpha Mask

## What a mask does differently than opacity
An `opacity` change dims everything uniformly. A `mask-image` gradient instead makes visibility itself vary across the element — only the *alpha channel* of the gradient matters, not its color. `black` in the gradient means fully visible, `transparent` means fully hidden, and anything in between fades proportionally. This is the right tool specifically for "this content should taper off at the edge," where a plain overlay-gradient-on-top would work for a solid background but fails the moment the content underneath is an image or variable in color.

## Horizontal fade (for a marquee or horizontally scrollable row)
```css
mask-image: linear-gradient(to right, transparent, black 15%, black 85%, transparent);
-webkit-mask-image: linear-gradient(to right, transparent, black 15%, black 85%, transparent);
```

## Vertical fade (for a long list or a tall image bleeding into a section)
```css
mask-image: linear-gradient(to bottom, transparent, black 15%, black 85%, transparent);
-webkit-mask-image: linear-gradient(to bottom, transparent, black 15%, black 85%, transparent);
```

Both directions follow the same shape: transparent → solid → solid → transparent, with the middle plateau being the fully-visible zone and the two ends being where the fade happens.

## Always ship both properties
`-webkit-mask-image` is required for Safari even in recent versions — the unprefixed `mask-image` alone silently does nothing there. Ship both every time, not just when a bug report shows up.

## Tuning
- **Direction** — `to right` / `to left` / `to bottom` / `to top`, or any angle.
- **Fade width** — move the `15%`/`85%` stops further apart to shrink the visible plateau (wider fade) or closer together to shrink the fade itself (sharper edge).
- **Fade softness** — replace `transparent` with a partially-opaque `rgba(0,0,0,0.2)` for a gentler taper instead of a hard fade to nothing.

## Where this silently fails to do anything visible
- **Nothing behind the mask.** A mask can only reveal or hide what's already there — if the element has no content or background to begin with, the mask has nothing to act on and the result looks like nothing changed.
- **Missing the Safari prefix.** The most common way this "doesn't work" — it works everywhere except Safari, because `-webkit-mask-image` was omitted.
- **Masking a container whose content is `overflow: hidden` but has no actual visible pixels near the edge** — masking clips visibility, it doesn't create content that wasn't there.

## Before applying, confirm
- Which direction the fade should run.
- How wide the fade zone needs to be relative to the element's total size.
- Whether this is fading an image, text, or a background — that affects whether a hard `transparent` edge or a softer `rgba` taper reads better.
