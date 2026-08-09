---
name: word-by-word-reveal
description: Use for a restrained, editorial word-by-word text reveal — each word fades and rises into place as it enters the viewport. Fits hero headlines, pull quotes, and short section intros that want a cinematic, premium entrance rather than a plain fade.
---

# Word-by-Word Reveal

## When this is the right call
Short text only — headlines, subheads, a pull quote, a one-line label. Staggering by *word*, not by letter, is what keeps this feeling calm and premium instead of gimmicky; letter-by-letter reads as a toy, word-by-word reads as intentional pacing.

## Non-negotiables before the motion
This pattern only works if it degrades gracefully, so build the fallback first, the animation second:
- Text must be fully visible and readable with JavaScript disabled — never hide content and rely on JS to reveal it.
- The DOM gets split into per-word spans for animation, but a screen reader should still hear the original sentence, not word-fragments with pauses — set `aria-label` on the container to the full text and mark each word span `aria-hidden`.
- Respect `prefers-reduced-motion`: skip straight to the fully-visible end state, no transition at all.
- Fires once. Scrolling past and back should not replay it.

## Motion values
| Property | Value |
|---|---|
| Start | `opacity: 0`, `translateY(20px)` |
| End | `opacity: 1`, `translateY(0)` |
| Duration | `0.8s` |
| Ease | `cubic-bezier(0.16, 1, 0.3, 1)` (a soft decelerate) |
| Per-word stagger | `0.06–0.08s` |
| Trigger | ~20% of the element visible |

## Markup and styling
```html
<h1 class="reveal-words" data-reveal-words>
  Build interfaces that feel calm and considered.
</h1>
```

```css
.reveal-words { visibility: visible; }

/* Only hide once JS has actually split the words — never hide by default */
html.js .reveal-words[data-reveal-words]:not(.is-ready) {
  opacity: 0;
}

.reveal-words__word {
  display: inline-block;
  opacity: 0;
  transform: translate3d(0, 20px, 0);
  transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1),
              transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
  transition-delay: calc(var(--i) * 0.07s);
  will-change: opacity, transform;
}

.reveal-words.is-visible .reveal-words__word {
  opacity: 1;
  transform: translate3d(0, 0, 0);
}

@media (prefers-reduced-motion: reduce) {
  html.js .reveal-words[data-reveal-words]:not(.is-ready),
  .reveal-words__word {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

## The splitter, and why it's written this way
```js
document.documentElement.classList.add("js")

function splitIntoWords(el) {
  if (el.dataset.revealReady === "true") return

  const original = el.textContent || ""
  const parts = original.split(/(\s+)/)   // keep whitespace as its own token
  let i = 0

  el.textContent = ""
  el.setAttribute("aria-label", original.trim())

  parts.forEach((part) => {
    if (!part.trim()) {
      el.appendChild(document.createTextNode(part))  // preserve real spaces
      return
    }
    const span = document.createElement("span")
    span.className = "reveal-words__word"
    span.setAttribute("aria-hidden", "true")
    span.style.setProperty("--i", i)
    span.textContent = part
    el.appendChild(span)
    i += 1
  })

  el.dataset.revealReady = "true"
  el.classList.add("is-ready")
}
```
Three deliberate choices here: splitting on `/(\s+)/` instead of `split(" ")` keeps whitespace as real text nodes rather than losing it, so word spacing survives untouched. Building nodes with `createElement`/`textContent` instead of `innerHTML` avoids re-parsing (and avoids accidentally executing) whatever the source text contains. And the `aria-label` is set on the *original* string before splitting, so assistive tech reads the sentence as written, never the fragmented spans.

```js
function initWordReveals(selector = "[data-reveal-words]") {
  const elements = Array.from(document.querySelectorAll(selector))
  const reduceMotion = window.matchMedia("(prefers-reduced-motion: reduce)").matches

  if (reduceMotion || !("IntersectionObserver" in window)) {
    elements.forEach((el) => el.classList.add("is-ready", "is-visible"))
    return
  }

  const observer = new IntersectionObserver((entries, io) => {
    entries.forEach((entry) => {
      if (!entry.isIntersecting) return
      entry.target.classList.add("is-visible")
      io.unobserve(entry.target)
    })
  }, { threshold: 0.2, rootMargin: "0px 0px -10% 0px" })

  elements.forEach((el) => {
    splitIntoWords(el)
    observer.observe(el)
  })
}

document.addEventListener("DOMContentLoaded", () => initWordReveals())
```
Note the reduced-motion / no-`IntersectionObserver` branch skips the split animation path entirely rather than just disabling the CSS transition — that's what guarantees the fallback text is exactly the original sentence, not a still-split-but-static version of it.

## In a component framework
Run the splitter after mount (`useEffect`/`onMount`), and disconnect the observer on unmount/route change — the same lifecycle concern as any IntersectionObserver-based reveal. If using Framer Motion or GSAP instead of raw CSS transitions, keep the same tokens rather than reinventing them: `y: 20, opacity: 0, duration: 0.8, ease: [0.16, 1, 0.3, 1], stagger: 0.06–0.08, once: true`.

## What keeps this feeling premium instead of gimmicky
- Animate only `transform` and `opacity` — no rotation, no bounce, no blur added on top.
- Keep the vertical offset small (20px, not 60px); a bigger throw reads as playful, not premium.
- Never split text that contains a link, button, or other meaningful inline markup — the splitter assumes plain text.
- If word-wrap position matters visually, initialize after web fonts have loaded, not before — fonts swapping after the split can reflow the stagger pattern.

## Pre-ship checklist
- Visible and readable with JavaScript off.
- Each word starts at `translateY(20px)` / `opacity: 0` and animates once.
- Scrolling away and back does not retrigger it.
- A screen reader announces the full original sentence, not fragments.
- `prefers-reduced-motion` users see static, immediately-readable text.
