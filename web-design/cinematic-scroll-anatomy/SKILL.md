---
name: cinematic-scroll-anatomy
description: Use when a page (portfolio, product story, editorial landing page) needs to read as one premium, scroll-driven experience — a preloader, a masked hero reveal, sticky card stacks that layer and recede, and pinned/scrubbed product-story scenes — rather than a static page with a few scroll animations sprinkled in. Assumes the underlying timeline/ScrollTrigger mechanics are already covered elsewhere; this skill is about page-level anatomy and choreography, not the animation API basics.
---

# Cinematic Scroll Anatomy

This sits above the animation library itself. It assumes the timeline and scroll-trigger fundamentals are already handled (see a general motion-timelines skill for that) and focuses on how a *whole page* gets composed into one cinematic sequence instead of a series of disconnected scroll effects.

## The feel this targets, and what breaks it
The reference point is premium editorial and product-launch sites: controlled pacing, layered depth, transitions that feel authored rather than default. It breaks the moment any of these show up: bounce/elastic easing (reads as playful, not premium, unless the brand actually is playful), aggressive scale jumps, more than one or two scroll effects firing simultaneously, or scroll hijacking that fights the reader's own scroll input.

## Page anatomy, in order
1. **Preloader** — a brief branded loading state with a progress indicator, then a fade or slide handoff into the hero. Skip it entirely under `prefers-reduced-motion` rather than showing a faster version of it.
2. **Hero** — a masked or split-text headline reveal, paired with subtle image/background parallax and a quiet scroll cue.
3. **Kinetic intro** — one passage of word-by-word or line-by-line reveal, used once near the top, not repeated throughout the page.
4. **Story sections** — ordinary scroll-triggered fade/blur-in reveals for supporting content; these play once and never re-trigger on a small scroll wiggle.
5. **A sticky stack** — for a set of comparable items (projects, features, steps): each card pins, then scales down and recedes as the next one arrives, so the stack reads as depth rather than a plain list.
6. **A pinned, scrubbed scene** — reserved for the one moment in the page that's genuinely about scroll-linked transformation (an image sequence, a product state change), not used as a default for every section.
7. **Footer handoff** — a slow parallax or upward reveal, so the page's ending feels considered rather than abrupt.

## Choreography patterns worth naming
- **Masked split-text**: split into per-word or per-line spans inside an overflow-hidden wrapper, then animate the inner span up through the mask — this reads as sharper and more "produced" than a plain fade. Keep the original text intact via `aria-label` on the wrapper, since the visible markup gets split into non-semantic spans that a screen reader shouldn't read word-by-word.
- **Sticky stack depth**: as the next card's top edge crosses roughly 75–80% of the viewport, ease the current card down in scale and opacity so it visibly recedes. The depth cue comes from both properties changing together, not either alone.
- **Section handoff**: let the outgoing section scale, blur, or translate into the next one instead of cutting abruptly — this is the difference between sections that feel connected and sections that feel like a slideshow.
- **Parallax, used narrowly**: reserve it for the hero background and the footer reveal. Applying it everywhere is what makes a page feel busy instead of cinematic.

For the actual duration, easing, and stagger numbers behind these patterns, and for performance/reduced-motion handling in general, lean on a dedicated motion-tokens skill rather than re-deriving them per page. This anatomy is about *where* each pattern goes, not the numeric tuning behind it.

## Tuning the pinned/scrubbed scenes specifically
- Pin duration should match how much actually needs to happen in that scene: extend it for a dense multi-state product walkthrough, keep it short for one or two state changes. An overlong pin with nothing new happening is what makes a page feel stuck rather than paced.
- A directly-scrubbed timeline (progress tied 1:1 to scroll) suits a precise technical walkthrough; a slightly eased scrub suits a more cinematic, film-like feel. Pick one per scene, deliberately.
- Reserve the highest-impact transitions for section handoffs specifically, not for every minor content change inside a section, or the page loses its sense of hierarchy.

## Before building
- Static site or single-page app? SPA route changes need every scroll-trigger instance explicitly torn down on unmount, or triggers from an unmounted route keep firing.
- Which sections actually need scrubbed/pinned treatment versus a simple one-time reveal? Naming this up front prevents scrubbing from becoming the default for everything.
- Does the page need to stay readable with JavaScript disabled, and is there a real reduced-motion path, not just a faster version of the same motion?

## QA checklist before shipping
Content is readable with JavaScript off. Reduced-motion users see static content with no smooth-scroll layer. One-time reveals don't re-trigger. Sticky stacks never overlap the footer or trap scrolling. The preloader exits even if an image loads slowly. Layout-affecting content (fonts, async images) triggers a re-measure of scroll positions. Mobile either simplifies or drops pinning if it's costing frame rate.
