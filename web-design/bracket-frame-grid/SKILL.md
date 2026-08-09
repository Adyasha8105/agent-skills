---
name: bracket-frame-grid
description: Use when a layout should feel precise, technical, and editorial — thin visible section borders, L-shaped corner brackets, strict grid alignment, and a barely-there diagonal texture. Good fit for structured, system-like, or spec-sheet-style pages rather than soft/card-based ones.
---

# Bracket Frame Grid

## The idea
Every section is a rectangle snapped to the same grid, outlined with a thin border, and marked at each corner with a small L-shaped bracket — like a technical spec sheet or camera viewfinder overlay. The structure itself is the visual interest; there are no floating cards, soft shadows, or uneven margins to distract from it.

## Rules that keep it coherent
1. Define the parent grid once. Every section snaps to the same columns and rows — nothing floats free of it.
2. One border color, one bracket color, one spacing scale, used everywhere. Mixing weights between adjacent frames breaks the "system" feeling immediately.
3. Borders stay thin — 1px, low contrast. This is a quiet structure, not a bold one.
4. A diagonal texture can sit in the background at very low opacity (under ~5%) — it should read as surface tension, not a pattern anyone consciously notices.
5. Padding, heading position, and media edges all align to the same rhythm across every section.

## Tokens
```css
:root {
  --frame-bg: #f7f7f4;
  --frame-surface: rgba(255, 255, 255, 0.62);
  --frame-line: rgba(24, 24, 27, 0.14);
  --frame-line-strong: rgba(24, 24, 27, 0.34);
  --frame-texture: rgba(24, 24, 27, 0.035);
  --frame-gap: 16px;
  --frame-pad: clamp(16px, 2vw, 28px);
  --frame-corner: 18px;
}
```

## The parent grid carries the alignment
```css
.bracket-grid {
  min-height: 100vh;
  padding: var(--frame-gap);
  background:
    repeating-linear-gradient(135deg, transparent 0 11px, var(--frame-texture) 11px 12px),
    var(--frame-bg);
  display: grid;
  grid-template-columns: repeat(12, minmax(0, 1fr));
  gap: var(--frame-gap);
}
.bracket-grid > * { min-width: 0; }
```

## Each section is the same box
```css
.frame {
  position: relative;
  border: 1px solid var(--frame-line);
  background: var(--frame-surface);
  padding: var(--frame-pad);
  overflow: hidden;
}
```

## Corner brackets without extra markup
The trick: stack eight small background-image gradients on one element, each one drawing a single short line positioned at one corner edge. This draws four L-shapes using pure CSS backgrounds — no extra `<div>`s per corner, and it stays crisp at any size since it's not an image asset.
```css
.frame-brackets {
  background:
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) left top / var(--frame-corner) 1px no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) left top / 1px var(--frame-corner) no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) right top / var(--frame-corner) 1px no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) right top / 1px var(--frame-corner) no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) left bottom / var(--frame-corner) 1px no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) left bottom / 1px var(--frame-corner) no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) right bottom / var(--frame-corner) 1px no-repeat,
    linear-gradient(var(--frame-line-strong), var(--frame-line-strong)) right bottom / 1px var(--frame-corner) no-repeat,
    var(--frame-surface);
}
```

## Spans instead of ad hoc widths
```css
.span-12 { grid-column: span 12; }
.span-8  { grid-column: span 8; }
.span-6  { grid-column: span 6; }
.span-4  { grid-column: span 4; }

@media (max-width: 760px) {
  .bracket-grid { grid-template-columns: 1fr; }
  .span-12, .span-8, .span-6, .span-4 { grid-column: 1 / -1; }
}
```

## Example
```html
<main class="bracket-grid">
  <section class="frame frame-brackets span-12">...</section>
  <section class="frame frame-brackets span-8">...</section>
  <aside   class="frame frame-brackets span-4">...</aside>
  <section class="frame frame-brackets span-6">...</section>
  <section class="frame frame-brackets span-6">...</section>
</main>
```

## Taste calls
- Corners stay square, or very lightly rounded only if the wider product already uses rounded corners elsewhere.
- Never mix border weights between sections that sit next to each other.
- No heavy shadows — the visible frame line is the only depth cue this pattern needs.
- Keep the diagonal texture layer below roughly 5% opacity; the layout should still read cleanly with that layer removed entirely.
- Bracket size stays identical across every major section — a bracket that's bigger on one section than another looks like a bug, not a variation.

## Quick self-check before shipping
- Do section edges line up vertically and horizontally against the grid?
- Is every frame using the same border weight, padding, and bracket scale?
- Are the gaps between frames consistent on both desktop and mobile?
- Is the diagonal texture only visible on close inspection, not at a glance?
