---
name: globe-gl-data-viz
description: Use when visualizing geographic data on a 3D globe with globe.gl (WebGL/Three.js under the hood) — points, arcs, polygons, heatmaps, labels. Covers setup, chaining data layers, and the container-sizing gotcha that causes an invisible globe.
---

# Globe.GL Data Visualization

## The API shape: one object, chained configuration
Every globe.gl instance is a single object where each method both sets a property and returns the instance, so configuration reads as a chain rather than a series of separate statements:
```js
import Globe from 'globe.gl'

const myGlobe = new Globe(document.getElementById('globe'))
  .globeImageUrl(myImageUrl)
  .pointsData(myData)
```
Or via a plain script tag instead of a module bundler:
```html
<script src="//cdn.jsdelivr.net/npm/globe.gl"></script>
<script>
  const myGlobe = new Globe(document.getElementById('globe'))
    .globeImageUrl(myImageUrl)
    .pointsData(myData)
</script>
```

## The gotcha that makes a globe render as nothing
The globe fills whatever size its parent container resolves to — it doesn't have an intrinsic size of its own. A container with no explicit height (common with a `div` that hasn't been given `height` in CSS) resolves to zero height, and the globe silently renders into a 0×0 canvas. Before debugging data or layers, confirm the container actually has a real, non-zero size via CSS.

## Layering data on top of the base globe
Each data layer is its own chained method, expecting an array of plain objects with whatever field names get mapped in a matching `*Field` accessor. A points-plus-arcs example, showing the field-mapping pattern:
```js
myGlobe
  .pointsData(cities)
  .pointLat(d => d.lat)
  .pointLng(d => d.lng)
  .pointColor(d => d.color)
  .pointAltitude(0.01)
  .arcsData(routes)
  .arcStartLat(d => d.startLat)
  .arcStartLng(d => d.startLng)
  .arcEndLat(d => d.endLat)
  .arcEndLng(d => d.endLng)
  .arcColor(d => d.color)
```
The accessor functions (`d => d.lat`) are what make the data shape flexible — the library never assumes a fixed field name, it always asks how to read each value.

## Layers worth knowing exist
Points, arcs, polygons (for country/region shading), paths, heatmaps and hex-bins (for density data), labels or arbitrary HTML elements pinned to coordinates, and full custom 3D objects for anything the built-in layers don't cover.

## Practical performance notes
- The container's CSS size *is* the globe's size — there's no separate size property fighting it.
- On mobile, cut point count and/or point size before cutting anything else; dense point layers are usually the first thing to cause frame drops on weaker GPUs.
- A darker globe texture reads better under neon/glowing data overlays — a bright default texture visually competes with glowing arcs or points instead of setting them off.

## Before building, confirm
- Which layers are actually needed (points, arcs, polygons, labels) — pulling in layers "just in case" adds render cost for nothing shown.
- Target globe size on desktop versus mobile — this is a CSS decision on the container, not a globe.gl option.
- Whether drag/rotate interaction is wanted, or a static, non-interactive globe is the goal.
- Plain HTML/JS, or a framework binding (`react-globe.gl` for React, etc.)?
