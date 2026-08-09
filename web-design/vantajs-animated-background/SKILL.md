---
name: vantajs-animated-background
description: Use for a quick animated WebGL background effect (waves, fog, net, birds) behind a hero section, without hand-building a Three.js scene. Covers setup, resize/cleanup lifecycle, and the pitfalls that make it invisible or a performance problem.
---

# Vanta.js Animated Background

## What it actually is
Vanta injects a `<canvas>` into a target container and renders one pre-built effect into it — most of its effects run on Three.js under the hood, so it's really "a curated set of Three.js scenes with a simple options API," not a separate rendering technology. That's the tradeoff: fast to reach a polished result, but less control than hand-authoring the scene.

## Setup
```html
<div id="hero" style="height: 70vh;"></div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vanta/dist/vanta.waves.min.js"></script>
<script>
  const effect = VANTA.WAVES({ el: "#hero", color: 0x0b1220, shininess: 40, waveHeight: 16, zoom: 0.9 })
</script>
```
Three.js has to be available (either the CDN script above, or passed explicitly as `THREE` in the options object, depending on the bundler setup) — Vanta's effect bundles assume it's there rather than including their own copy.

## The lifecycle methods
- `effect.setOptions({ color: 0xff88cc })` — update parameters on a running effect without tearing it down.
- `effect.resize()` — call when the container's size changes; Vanta doesn't watch this automatically.
- `effect.destroy()` — tears the WebGL context and canvas down. Skipping this in an SPA leaks a running render loop and a GPU context every time the component using it unmounts.

## Cleanup in a component framework
```js
useEffect(() => {
  const effect = VANTA.WAVES({ el: heroRef.current, color: 0x0b1220 })
  return () => effect.destroy()
}, [])
```
Create inside the mount effect, keep the instance itself (not just a boolean flag) so the cleanup function has something concrete to call `.destroy()` on.

## Where this silently fails or gets expensive
- **Container has no resolved size** — if the target element has no explicit height (or isn't otherwise laid out to have one), the canvas Vanta injects also has no size, and nothing renders. This is the single most common "it's not showing up" report, and it's a CSS issue, not a Vanta bug.
- **Multiple WebGL canvases on one page** — each Vanta effect is its own WebGL context; stacking several on one page multiplies GPU load fast. Keep it to one, or at most two, per page.
- **Weaker/mobile GPUs struggling** — provide a static fallback background color or image, and seriously consider disabling the effect entirely on small screens rather than running a degraded version of it.
- **Text sitting on top of a moving background** — the same legibility problem as any full-bleed visual behind content: a moving, colorful background under text needs either a darkening overlay/gradient or careful color choice in the effect itself, or the text becomes hard to read as the animation moves through different regions of contrast.

## Before building, confirm
- Which specific effect (waves, birds, fog, net, and others) and what brand colors it should use.
- Whether it has to run on mobile at all — and if so, what quality/FPS tradeoff is acceptable rather than assumed.
- Whether text sits on top of it, which decides whether an overlay/gradient is needed for readability before the effect ships.
