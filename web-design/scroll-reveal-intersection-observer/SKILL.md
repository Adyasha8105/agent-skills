---
name: scroll-reveal-intersection-observer
description: Use when elements need to animate in as they enter the viewport (scroll-reveal, fade/slide-up-on-scroll, staggered section reveals) without pulling in an animation library. Built on IntersectionObserver plus CSS keyframes.
---

# Scroll Reveal via IntersectionObserver

## The mechanism
CSS keyframes define the motion; IntersectionObserver decides *when* to let it play. The animation starts paused (`animation-play-state: paused`) on every candidate element, and a single shared observer flips it to `running` the moment the element crosses into the viewport. This avoids polling scroll position on every frame — the browser only notifies on actual visibility changes.

```css
@keyframes reveal-in {
  from { opacity: 0; transform: translateY(30px); filter: blur(8px); }
  to   { opacity: 1; transform: translateY(0);    filter: blur(0);  }
}

.reveal {
  animation: reveal-in 0.8s ease-out 0.1s both;
  animation-play-state: paused;
}
.reveal.in-view {
  animation-play-state: running;
}
```

```js
function initScrollReveal(selector = ".reveal") {
  const observer = new IntersectionObserver((entries) => {
    for (const entry of entries) {
      if (!entry.isIntersecting) continue
      entry.target.classList.add("in-view")
      observer.unobserve(entry.target)   // remove this line to allow replay on re-entry
    }
  }, { threshold: 0.2, rootMargin: "0px 0px -10% 0px" })

  document.querySelectorAll(selector).forEach((el) => observer.observe(el))
  return observer
}
```

## Reuse one observer, not one per element
A single `IntersectionObserver` instance can watch any number of elements — instantiate it once per page (or per mounted component) and call `.observe()` on each target, rather than creating a new observer per element. The callback receives a batch of entries, so one observer scales to hundreds of elements at no extra cost.

## In a component framework, this needs a lifecycle
Unlike a plain static page, a component-based app (React, Svelte, Vue) mounts and unmounts this repeatedly. Two things a plain HTML version glosses over:
- Call `observer.disconnect()` on unmount, or every reveal target from a removed page keeps a dangling observer entry.
- The unrevealed state should be the CSS default, not something JS forces before paint — otherwise an element can flash fully visible and then jump backward into its "paused" state on the first frame.

## Customization knobs
- **Trigger timing** — `threshold` (how much of the element must be visible) and `rootMargin` (shrink/grow the effective viewport) control how early or late the reveal fires.
- **Replay vs. once** — skip the `unobserve()` call and instead toggle the class both ways (`entry.isIntersecting ? add : remove`) to let elements re-animate every time they cross into view.
- **Motion style** — the keyframes are the only thing defining what "reveal" looks like; swap `translateY`/`blur`/`scale` freely, the observer logic doesn't change.

## Where this breaks
- Keyframes referenced before they're declared in document order can cause a flash of the unanimated end-state on first paint — declare keyframes before any element that uses them is rendered.
- A typo between the keyframe name and the animation shorthand fails silently: no error, the element just never animates.
- If an element is already in the viewport at page load (e.g. above the fold), the observer's first callback fires almost immediately — decide up front whether above-the-fold content should skip the reveal entirely rather than have it look broken on load.

## Ask before building
- Once per element, or replay every time it re-enters view?
- How early should the reveal fire relative to entering the viewport?
- Fade, slide, blur, scale, or some combination?
