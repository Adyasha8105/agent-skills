---
name: matterjs-2d-physics
description: Use when adding 2D physics interactions with Matter.js — falling/colliding objects, draggable bodies, physics-driven decorative scenes. Covers the Engine/World/Render/Runner mental model, mouse interaction, and cleanup for SPA routes.
---

# Matter.js 2D Physics

## The four pieces, and why they're separate
Matter.js splits cleanly into four independent concerns, which matters once anything beyond a toy demo is being built:
- **Engine** — holds the physics simulation state (gravity, timing, collision detection). It knows nothing about pixels.
- **World** (`engine.world`) — the container of bodies and constraints the engine simulates. Also knows nothing about pixels.
- **Render** — one specific way to draw the world to a canvas, provided for convenience. Swappable for a custom renderer (e.g. drawing to a `<canvas>` inside a design system, or driving Three.js visuals from the same physics state).
- **Runner** — drives the timestep, calling `Engine.update` on a loop. Also swappable for a custom loop if the app already has one (e.g. syncing to `requestAnimationFrame` alongside other animation).

Understanding that Render and Runner are conveniences, not requirements, is what makes it possible to use Matter.js for its physics only, while handling drawing and timing some other way.

## Minimal working setup
```js
const { Engine, Render, Runner, Bodies, Composite } = Matter

const engine = Engine.create()

const render = Render.create({
  element: document.body,   // Matter creates and appends a <canvas> here
  engine,
  options: { width: 800, height: 600, wireframes: false },
})

const runner = Runner.create()
Runner.run(runner, engine)
Render.run(render)

const ground = Bodies.rectangle(400, 610, 810, 60, { isStatic: true })
const box = Bodies.rectangle(400, 200, 80, 80)

Composite.add(engine.world, [ground, box])
```
`isStatic: true` is what makes the ground immovable rather than something that itself falls under gravity — every body defaults to dynamic unless told otherwise. `wireframes: false` switches the built-in renderer from debug outlines to solid shapes.

## Adding interaction
```js
const { Mouse, MouseConstraint } = Matter
const mouse = Mouse.create(render.canvas)
const mouseConstraint = MouseConstraint.create(engine, { mouse })
Composite.add(engine.world, mouseConstraint)
render.mouse = mouse   // without this, Matter's own coordinate transforms won't account for the mouse
```
`MouseConstraint` is itself a constraint added to the world — dragging a body works by literally connecting it to the mouse position with a spring-like constraint while the pointer is down, not through some separate "drag" mode.

## Pausing when offscreen
The source pattern (`Render.run` + `Runner.run`) simulates continuously regardless of visibility, which wastes CPU on a decorative scene scrolled out of view. Pair it with an `IntersectionObserver` on the canvas's container:
```js
const observer = new IntersectionObserver(([entry]) => {
  if (entry.isIntersecting) {
    Runner.run(runner, engine)
    Render.run(render)
  } else {
    Runner.stop(runner)
    Render.stop(render)
  }
}, { threshold: 0 })

observer.observe(render.canvas)
```
This is the same visibility-driven pattern used for scroll-reveal animations elsewhere — reuse it rather than inventing a separate offscreen-detection mechanism.

## Cleanup in an SPA
On unmount/route change:
- `Runner.stop(runner)` and `Render.stop(render)` to halt both loops.
- Remove `render.canvas` from the DOM (Matter doesn't do this automatically — it created the element, but doesn't track when to remove it).
- If the scene won't be reused, let `engine`/`world` fall out of scope rather than trying to manually null every body reference.

## Before building, confirm
- Target canvas size, and whether it needs to scale/respond to viewport size.
- Matter's built-in `Render`, or a custom renderer driven by the same physics state?
- Does anything need mouse/touch drag interaction, or is this purely decorative/non-interactive?
- Should the simulation pause when scrolled offscreen? (Usually yes, for anything decorative.)
