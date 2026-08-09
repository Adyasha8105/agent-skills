---
name: scroll-narrative-world
description: Use when a long-form article, case study, product journey, or brand story needs to become one continuous scroll-driven page instead of a stack of ordinary sections — deciding between a scrubbed video, a real-time 3D world, or a data/typography-driven page, then holding that choice to a small set of measurable beats instead of a paragraph-per-section retelling.
---

# Scroll-Narrative World

The failure mode this guards against: turning every paragraph of a source document into its own section, which produces a long page with no throughline instead of one connected journey. The fix is compressing the source into a small number of deliberate beats and committing to one renderer for the whole thing.

## Pick exactly one renderer
These are competing choices, not a stack — mixing them mid-page breaks the sense of one continuous world:

| Renderer | Best fit | What it costs you |
|---|---|---|
| Scrubbed video | Cinematic realism, real places or products, a pre-rendered camera move | Heavier assets, seek/scrub tuning |
| Real-time 3D world | An object, place, planet, or system that benefits from spatial camera control | WebGL performance work and a fallback path |
| Data / typography | Reports, launches, metrics, editorial argument | Less photographic spectacle, but lighter and more accessible |

A story that's mostly proof and numbers belongs in the data/typography lane. A story built around one spatial metaphor belongs in the 3D lane. A story that's fundamentally about a place or a look belongs in video.

## Compress the source into 5–7 beats
Resist the urge to give every paragraph its own scene. A workable beat structure:
1. **Hook** — the promise or the surprising claim.
2. **Old way** — the friction or assumption being displaced.
3. **New rule** — the idea that changes the approach.
4. **Mechanism** — how it actually works.
5. **Proof** — the strongest available evidence.
6. **Payoff** — the end state after the change.
7. **Action** — the one thing a reader should do next.

Combine beats that repeat the same argument. Keep the primary call-to-action to the final beat unless the source genuinely earns an earlier one.

## Write the direction once, before any scene
A short style brief prevents the page from drifting visually beat-to-beat:
- **Mood**, in three precise adjectives — not "modern" or "clean," pick words specific enough to rule things out.
- **World metaphor** — one place or system that can plausibly hold every beat (a lab, an orbit, a river, a city).
- **Palette** — 4–6 named colors, one dominant field, one accent.
- **Motion grammar** — one verb for how the camera or scroll moves: glide, orbit, crane, track, dive. Pick one and stay consistent for the whole page.

## The non-negotiables, regardless of renderer
- Preserve the source's actual facts and sequence. This is a presentation layer, not a rewrite of the argument, and nothing gets fabricated as proof.
- Scrolling stays native and reversible, in both directions, at any speed. Never hijack the wheel or trap the scroll.
- One primary call-to-action; keep navigation and other controls quiet so they don't compete with the story.
- A reduced-motion path exists and is genuinely legible, not just technically present.

## Verifying it actually works
Check, in order: does each beat still advance the thesis; is the correct visual state active at every scroll position; does scrolling backward restore the exact prior state, not an approximation; is there unnecessary work still running after the scroll settles; does it hold up at common breakpoints and under `prefers-reduced-motion`; and does the chosen renderer actually get used end-to-end, rather than partially abandoned partway through the build.

## What to hand back
The compressed beat list, the style brief, which renderer was chosen and why, the working page, and a plain note of any known limitation — a scroll-driven page that quietly breaks on one breakpoint is worse than a page that admits the gap.
