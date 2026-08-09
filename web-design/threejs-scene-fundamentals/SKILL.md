---
name: threejs-scene-fundamentals
description: Use when building or debugging an interactive 3D scene on the web with Three.js — the scene/camera/renderer/mesh mental model, asset loading, controls, and the cleanup/performance mistakes that cause a scene to leak memory or melt mobile GPUs. For deciding *whether* a WebGL hero fits a landing page at all, see a WebGL-direction skill instead; this one assumes 3D is already the right call and covers building it correctly.
---

# Three.js Scene Fundamentals

## When real 3D is the right tool
Reach for this when the page needs genuine 3D behavior: a product that spins and can be inspected from any angle, an interactive scene the pointer actually affects, shader-driven material effects, or true 3D data visualization. If the ask is closer to "an animated background behind a hero," a lighter-weight approach usually fits better and costs less to maintain — save a full scene graph for when 3D control is actually needed.

## The mental model
Every scene is four pieces working together: a **Scene** (the root graph everything gets added to), a **Camera** (perspective for most product/hero work, orthographic for flat/technical views), a **Renderer** (drawing the scene through the camera onto a canvas), and **Meshes** (a geometry paired with a material, the actual visible objects). Add lights whenever a material isn't fully unlit — an object with no light source and a standard material renders as solid black, which is a common first surprise.

The render loop has the same shape every time: a per-frame callback updates whatever's time-based (rotations, controls, animation mixers), then draws the scene through the camera. Nothing animates on its own; the loop is what makes the scene move.

## Setup that's easy to get wrong
- Set the renderer's pixel ratio explicitly and cap it around 2. An uncapped ratio on a high-density mobile display multiplies the pixel-fill cost for no visible benefit, and is a common cause of mobile stutter.
- Resize handling has two parts, not one: resizing the renderer's output **and** updating the camera's aspect ratio plus its projection matrix. Skipping the second half produces a stretched or cropped scene on resize, even though the canvas itself looks the right size.
- Loading heavy assets (large 3D models, uncompressed textures) blocks the scene's first meaningful paint. Compress textures and geometry, and size texture maps to what the object will actually appear at on screen, not the source resolution.

## Cleanup is not optional in a single-page app
Every geometry, material, and texture holds GPU memory until it's explicitly released. On a route change in an SPA, skipping that release is exactly what causes a scene to work fine the first time and then crash or degrade after a few navigations back to the same page. Cancel the render loop and remove any resize or pointer listeners at the same time as disposing the GPU resources — a scene that's still rendering after its page has been left is doing invisible, wasted work at best.

## A minimal working scene, in plain terms
Build it in this order and it stays debuggable at every step:
1. Create the renderer against a canvas element, with antialiasing on and the pixel ratio capped.
2. Create the scene and a perspective camera, and position the camera back far enough to see the origin.
3. Create one mesh (a simple geometry plus a standard material) and add it to the scene.
4. Add an ambient light for base visibility and one directional "key" light for shape and shading — without at least these two, the mesh above will render as a flat silhouette.
5. Wire up a resize handler that updates both the renderer size and the camera's aspect/projection.
6. Start the render loop: update the mesh's rotation (or whatever is animated) by elapsed time, then render.

Under `prefers-reduced-motion`, skip the render loop entirely and draw a single still frame instead of just slowing the same animation down — a slow spin is still motion.

## Controls and loaders, by use case
Reach for orbit-style controls for product-inspection scenes and for debugging camera placement, first-person-style controls for navigation through a space, or plain pointer-event handlers for anything more custom than either provides. For assets, use a model loader for 3D geometry, a texture loader for images, and a compressed-geometry loader when file size on a 3D model matters — pull these in only when the scene actually loads external assets, not by default.

## Before building
Is this decorative (a hero moment) or functional (a real product viewer someone interacts with)? What's the target device floor — does it need to hold up on an older phone? What asset formats are actually available, and is there a file-size ceiling? Is a reduced-motion fallback required, or just expected?
