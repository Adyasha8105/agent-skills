---
name: unicorn-studio-embed
description: Use when embedding a Unicorn Studio scene (a no-code, designer-authored WebGL visual with built-in scroll/hover/entrance interactivity) into a site. Covers the attribute-driven embed pattern, performance tuning knobs, and the WebGL-context ceiling that limits how many scenes one page can hold.
---

# Unicorn Studio Embed

## What this actually solves
Unicorn Studio is a scene editor for people who want a custom, designer-authored WebGL visual — layered with text/image/video, with interactivity (hover, scroll progress, entrance) already wired up in the editor — without hand-writing shaders or a Three.js scene. The output is either a small SDK-loaded embed or a JSON/code export for self-hosted loading, depending on plan. This is the "designed effect, no code" end of the spectrum; hand-built Three.js sits at the other end when more control is needed.

## The embed is attribute-driven, not a JS call per scene
Load the SDK once, then every scene on the page is just a container element with data attributes — no per-scene JavaScript needed:
```html
<div style="width: 100%; height: 420px" data-us-project="YOUR_PROJECT_ID"></div>
```
The SDK script (loaded in `<head>` or just before `</body>`, depending on whether the scene is above the fold and needs to appear immediately) scans the page for `data-us-project` attributes and initializes each one — `UnicornStudio.init()` is called once regardless of how many scene containers exist.

## Performance-tuning attributes
```html
<div
  style="width: 100%; height: 420px"
  data-us-project="YOUR_PROJECT_ID"
  data-us-lazyload="true"
  data-us-production="true"
  data-us-scale="0.75"
  data-us-dpi="1.25"
  data-us-fps="45"
></div>
```
- `data-us-scale` — renders at a fraction of the container's resolution, the single biggest performance lever.
- `data-us-dpi` — caps the resolution multiplier, same purpose as clamping device pixel ratio anywhere else on the web.
- `data-us-fps` — caps the frame rate; a below-fold decorative scene rarely needs to run at a full 60fps.
- `data-us-lazyload` — defers initialization until the scene is actually about to be visible, instead of paying the WebGL setup cost for something scrolled far below the fold.

## The hard ceiling: WebGL contexts are a shared, finite resource
Browsers cap the number of simultaneous WebGL contexts a page can hold at roughly 16 — and that ceiling is shared across *every* WebGL usage on the page, not just Unicorn Studio scenes. Staying under 10 scenes per page is a safety margin, not an arbitrary preference; crossing the real ceiling doesn't degrade gracefully; contexts start failing to initialize.

## Where this goes wrong
- **Container with no resolved size** — same failure as any canvas-based embed: no explicit width/height on the container means the scene has nothing to render into and appears blank.
- **Too many scenes stacking up WebGL contexts** — see above; this compounds with any other WebGL usage already on the page (a Vanta background, a Three.js hero, a globe), not just other Unicorn scenes.
- **Editor preview not matching reality** — many site builders (Webflow, Framer, and others) don't render the embed inside their own edit-mode canvas; the scene only becomes visible in an actual preview or published page. Don't debug a "broken" embed inside the builder's editor view.

## Before embedding, confirm
- Target platform — Webflow, Framer, Wix, Figma Sites, or a hand-coded site — since script placement conventions differ.
- Whether the scene sits above the fold, which decides both script placement and whether `data-us-lazyload` should be used at all (lazy-loading something already visible on load defeats the point).
- Mobile support requirement and what quality/FPS tradeoff is acceptable there.
- How many scenes the page will have in total, and whether a JSON export (for self-hosting) is available on the current plan versus relying on the hosted SDK.
