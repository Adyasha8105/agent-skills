---
name: site-build-kickoff
description: Use when starting a brand-new website or web app from scratch, before writing any code. Walks through the design brief, page inventory, and which of this skill library's other skills apply where and in what order, producing a concrete build plan instead of ad hoc page-by-page decisions.
---

# Site Build Kickoff

This is the entry point for a from-scratch build, not a technique of its own. Its job is to produce a short written plan — page list, visual direction, and which skill handles which part — before any screen gets generated. Skipping straight to "build the homepage" without this plan is what causes a site to drift into three different visual languages by page five.

## Step 1: Answer these before anything else
- What is the site/app, who is it for, and what's the single most important thing a first-time visitor should be able to do?
- What pages actually need to exist? (Not every site needs a pricing page; not every product needs a public marketing site at all.)
- What's the stack — plain HTML, React/Next, Vue, Svelte/SvelteKit, a site builder? This affects which situational skills even apply (some assume a component framework's mount/unmount lifecycle).
- Any constraints already fixed — an existing brand palette, an existing type system, a deadline that rules out heavy 3D/WebGL work?

If any of this is missing, ask before proceeding — the rest of this plan is built on these answers.

## Step 2: Lock the design brief once
Run `spec-driven-ui-prompting` against the whole site's direction, not a single screen: intent, structure, type, color, boundaries. This becomes the reference every later page gets checked against, so it only needs doing once, up front.

## Step 3: Choose exactly one visual theme and one structural layout
These two categories are competing directions, not a stack — picking one from each locks the site-wide system:

| Category | Options | Pick based on |
|---|---|---|
| Visual theme | `dark-glassmorphism-panel` + `dark-glass-workspace-layout` (dark, frosted, monochrome) vs. `warm-neutral-minimal-light` (calm warm-neutral light mode) | Brand tone — premium/technical vs. calm/approachable |
| Structural layout | `bracket-frame-grid` (technical/editorial) vs. `full-bleed-image-grid` (cinematic, image-led) | Whether the product has strong photography/media to lead with, or is more information/system-driven |

If neither pair fits (e.g. the brand is bright and colorful, not dark or beige), say so explicitly rather than forcing one of these two — this library's themes aren't the only valid answers, they're the two currently built out.

## Step 4: Map the page inventory to structural skills
For each page in the inventory from Step 1, assign the skill that owns its structure:

| Page type | Skill |
|---|---|
| Landing/marketing page | `single-offer-landing-page` |
| Pricing | `pricing-page-design` |
| An onboarding flow, roadmap, or any ordered process | `progress-line-scroll-story` |
| A trust/partner/integrations strip | `consistent-brand-logo-row` |
| Anything else (dashboards, detail pages, feeds) | No dedicated skill yet in this library — build directly from the Step 2 brief and Step 3 theme/layout choice |

Note pages with no dedicated skill explicitly in the plan rather than silently improvising their structure — that's exactly the kind of drift this kickoff step exists to prevent.

## Step 5: Decide situational effects per page, deliberately
Go through the situational group (`scroll-reveal-intersection-observer`, `gsap-motion-timelines`, `word-by-word-reveal`, `overflow-mask-word-reveal`, `cursor-spotlight-reveal`, `gradient-border-edge`, `edge-fade-alpha-mask`, `layered-progressive-blur`, `layered-elevation-shadows`) and assign each one to a specific page and element, or explicitly decide against it. A real site typically uses three or four of these total across the whole build — assigning all nine everywhere produces a page that feels like a demo reel, not a product.

## Step 6: Decorative/rare skills need an explicit reason
`globe-gl-data-viz`, `cobe-lightweight-globe`, `vantajs-animated-background`, `unicorn-studio-embed`, `matterjs-2d-physics` should only enter the plan if something in Step 1 specifically calls for that exact effect (real geographic data, a literal spinning-globe brand moment, a physics-driven decoration). Default to leaving all five out; add one only with a stated reason tied back to the brief.

## Step 7: Sequence the build
1. Foundation (`tailwind-utility-styling` if applicable, `product-motion-system` for the motion vocabulary) — established once, referenced everywhere after.
2. The chosen theme + structural layout, built as the site shell before individual pages.
3. Each page from the Step 4 table, built against the Step 2 brief.
4. Situational polish from Step 5, applied once structure and real content exist — not before.
5. Any Step 6 decorative element, last, since it's the least load-bearing part of the plan.

## What to hand back at the end of this step
A short written plan: the page list, the two Step 3 choices, the Step 4 table filled in, the Step 5 assignments (or explicit "none" for a page), and confirmation Step 6 was either skipped or justified. Get this confirmed before generating the first real screen.
