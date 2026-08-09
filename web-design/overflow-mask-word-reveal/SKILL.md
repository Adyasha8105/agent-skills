---
name: overflow-mask-word-reveal
description: Use for a scroll-triggered word reveal where each word rises up through a hidden overflow mask, like a blind lifting, rather than simply fading in. Pairs with GSAP ScrollTrigger. Fits headlines and section titles that want a sharper, more editorial reveal than a plain fade-and-rise.
---

# Overflow-Mask Word Reveal

## How this differs from a plain fade-and-rise
A basic word reveal fades opacity in while sliding a word up slightly. This pattern instead crops each word inside a small `overflow: hidden` wrapper and slides the word up *from below that crop line* — so instead of materializing in place, it looks like the word is rising up through a slit, cleanly cut off until it clears the mask. Same "staggered words entering" family, sharper and more mechanical-feeling result.

## The two-layer structure
Every word needs two nested elements, not one:
```html
<span class="word-mask" aria-hidden="true">   <!-- overflow: hidden, the crop line -->
  <span class="word">Word</span>              <!-- the thing that actually translates -->
</span>
```
The outer span defines where the word gets clipped; the inner span is what moves. Animate the *inner* span's `translateY`/`yPercent` — animating the outer span would move the crop line itself, not reveal anything through it.

## Motion values
| Property | Value |
|---|---|
| Scroll trigger point | text top reaches ~82% of viewport height |
| Duration | `0.7–0.9s` |
| Per-word stagger | `0.025–0.045s` (tighter than a plain fade reveal — the mask reads clearly even with fast stagger) |
| Offset | `yPercent: 110 → 0` (110, not 100, so the word starts fully below the crop with a small margin) |
| Ease | `power3.out` or `expo.out` |
| Replay | once |

## Markup and CSS
```html
<h1 class="mask-reveal" data-mask-reveal>
  Design systems that feel alive from the first scroll.
</h1>
```

```css
.mask-reveal { visibility: visible; }

/* Hide only after JS is confirmed active; never hide by default */
html.js .mask-reveal[data-mask-reveal] { visibility: hidden; }
html.js .mask-reveal.is-split { visibility: visible; }

.mask-reveal .word-mask {
  display: inline-block;
  overflow: hidden;
  vertical-align: top;
}

.mask-reveal .word {
  display: inline-block;
  transform: translateY(110%);
  will-change: transform;
}

@media (prefers-reduced-motion: reduce) {
  html.js .mask-reveal[data-mask-reveal] { visibility: visible; }
  .mask-reveal .word { transform: none; }
}
```

## Splitting the text — and why this version escapes HTML instead of building nodes
```js
document.documentElement.classList.add("js")
gsap.registerPlugin(ScrollTrigger)

function escapeHTML(value) {
  return value
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&#039;")
}

function splitIntoMaskedWords(el) {
  if (el.dataset.maskReady === "true") return

  const text = el.textContent.trim()
  el.setAttribute("aria-label", text)
  el.innerHTML = text
    .split(/(\s+)/)
    .map((part) => {
      if (!part.trim()) return part
      return `<span class="word-mask" aria-hidden="true"><span class="word">${escapeHTML(part)}</span></span>`
    })
    .join("")

  el.dataset.maskReady = "true"
  el.classList.add("is-split")
}
```
This version needs two nested wrapper elements per word, which is awkward to build with `createElement` calls — templating the markup as a string is simpler here, but that means `innerHTML` is back in play, so every piece of text has to go through `escapeHTML` first. That's the tradeoff: convenience of templating vs. having to actively guard against injecting whatever the source text happens to contain.

## Wiring it to scroll
```js
function initMaskedReveals(selector = "[data-mask-reveal]") {
  if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) return

  document.querySelectorAll(selector).forEach((el) => {
    splitIntoMaskedWords(el)
    const words = el.querySelectorAll(".word")

    gsap.set(el, { autoAlpha: 1 })
    gsap.fromTo(words,
      { yPercent: 110 },
      {
        yPercent: 0,
        duration: 0.8,
        ease: "power3.out",
        stagger: 0.035,
        scrollTrigger: { trigger: el, start: "top 82%", once: true },
      }
    )
  })
}

initMaskedReveals()
```

## Cleanup in a component framework
```js
useLayoutEffect(() => {
  const ctx = gsap.context(() => {
    initMaskedReveals("[data-mask-reveal]")
  }, rootRef)
  return () => ctx.revert()   // kills the ScrollTrigger instances created inside this context
}, [])
```
`gsap.context` scopes every animation and ScrollTrigger created inside the callback to `rootRef`, so `.revert()` on unmount tears all of it down in one call instead of manually tracking and killing each trigger.

## Taste calls
- Reserve this for short headlines and section intros — long paragraphs turn the mask effect into visual noise.
- Don't stack a blur on top unless the visual direction explicitly calls for it; the crop line is already doing the work.
- Stagger by word, not letter — letter-level masking on this technique reads as busy rather than sharp.
- If line-wrap position matters, initialize after web fonts have loaded — a late font swap can shift where words wrap and desync the stagger from the visual layout.
- Call `ScrollTrigger.refresh()` after any late-loading image or layout shift changes the page height, or trigger points will be measured against a stale layout.

## Pre-ship checklist
- Text stays hidden only until GSAP has actually initialized, then becomes visible via `autoAlpha: 1` — never permanently hidden by CSS alone.
- A screen reader announces the original full sentence via `aria-label`, not fragmented spans.
- Word spacing survives the split intact.
- `prefers-reduced-motion` users get static, fully visible text with no mask/motion at all.
- ScrollTrigger instances are torn down on route change in an SPA.
