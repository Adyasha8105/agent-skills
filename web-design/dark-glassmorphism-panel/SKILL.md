---
name: dark-glassmorphism-panel
description: Use for dark-mode glassmorphism — frosted, blurred surfaces with a subtle gradient glow border — while keeping text contrast readable. Covers the token set, the frosted-panel recipe, and the contrast pitfalls that make dark glass specifically harder to get right than light glass.
---

# Dark Glassmorphism Panel

## Why dark glass is harder than light glass
Frosted glass over a light background almost always stays readable — light passing through a blur tends to lighten, which text can sit on top of. Over a dark background, the same blur can crush contrast instead, especially once a glow or accent color gets layered in. Everything below is built around protecting text legibility first, glass effect second.

## Tokens
```css
:root {
  --bg-0: #020617;
  --bg-1: #0b1220;
  --glass-fill: rgba(15, 23, 42, 0.45);
  --glass-fill-strong: rgba(15, 23, 42, 0.62);  /* fallback for browsers without backdrop-filter */
  --text-main: #e2e8f0;
  --text-muted: #94a3b8;
  --accent: #60a5fa;
}
```

## The frosted panel itself
```css
.glass-panel {
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.08), rgba(255, 255, 255, 0.02));
  background-color: var(--glass-fill);
  border-radius: 24px;
  box-shadow: 0 20px 48px rgba(2, 6, 23, 0.45), inset 0 1px 0 rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(18px) saturate(140%);
  -webkit-backdrop-filter: blur(18px) saturate(140%);
}
```
Three things doing distinct work here: the faint top-to-bottom white gradient simulates a light catching the top edge of the glass; the `inset 0 1px 0` box-shadow adds a hairline highlight along the top edge specifically (glass reads as glass partly because of where light catches it, not just the blur); and `saturate(140%)` alongside the blur keeps whatever colors show through from washing out to gray, which plain blur alone tends to do.

## The border glow
This reuses the masked gradient-border technique — a `::before` pseudo-element with a `mask-composite: exclude` ring — tuned to this dark palette's colors instead of a neutral one:
```css
.border-gradient { position: relative; }

.border-gradient::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  background: linear-gradient(145deg,
    rgba(148, 163, 184, 0.28) 0%,
    rgba(96, 165, 250, 0.36) 45%,
    rgba(168, 85, 247, 0.3) 70%,
    rgba(148, 163, 184, 0.18) 100%);
  pointer-events: none;
}
```
(See the dedicated gradient-border skill for why the mask math works the way it does — this is the same mechanism, just re-colored for a dark surface.)

## Tailwind version
```html
<section class="relative rounded-3xl border-gradient bg-slate-950/45 backdrop-blur-xl p-8
  shadow-[0_20px_48px_rgba(2,6,23,0.45),inset_0_1px_0_rgba(255,255,255,0.12)]">
  <h2 class="text-slate-100 text-2xl font-semibold">Frosted panel</h2>
  <p class="text-slate-400 mt-2">Dark glass with a masked gradient border.</p>
</section>
```

## The contrast checklist that actually matters here
- Body text should never go lighter than roughly `#cbd5e1` — dark glass with a blur behind it eats contrast faster than a flat dark background does.
- Avoid pure black as the tint under the blur; a deep navy or charcoal alpha (like the tokens above) keeps some color information alive instead of the panel reading as a dead void.
- Keep glow/accent radius and intensity restrained — a strong glow behind text is one of the fastest ways to fail a contrast check on a dark glass surface specifically.
- Focus states need to be genuinely visible against a blurred backdrop — a subtle default outline that works fine on a solid background can disappear here; use a brighter ring or border-state change.
- Always define `--glass-fill-strong` as a solid fallback fill for environments without `backdrop-filter` support (or where it's disabled for performance) — without it, unsupported browsers get a transparent panel with unreadable text on whatever's behind it.

## Where this gets asked for
Dark hero sections with glowing bordered cards, converting an existing bright UI to dark glass without losing readability, adding a border glow to a frosted nav bar or modal, or giving a dark dashboard a more premium layered-depth feel. In all of these, build the token set and contrast checks first — the glass effect itself is the easy part.
