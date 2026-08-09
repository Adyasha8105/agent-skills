# agent-skills

A personal library of Claude agent skills for web design and UI work — each one a self-contained `SKILL.md` an agent can pick up when its description matches the task at hand.

Written to be general-purpose and reusable across projects, not tied to any single app.

## Layout

```
ui/           general UI-prompting technique
web-design/   layout, motion, and visual-style skills
```

## Installing

Claude Code discovers skills from a **flat** directory — each skill needs its own folder directly under a scanned location, not nested by category. Symlinking keeps edits here live everywhere instead of needing a re-copy.

**Globally** (available in every project on this machine):
```bash
SRC=/path/to/agent-skills
DEST=$HOME/.claude/skills
for dir in "$SRC"/ui/*/ "$SRC"/web-design/*/; do
  name=$(basename "$dir")
  [ -e "$DEST/$name" ] || ln -s "$dir" "$DEST/$name"
done
```

**Project-local** (only active inside one repo): same loop, with `DEST=<project>/.claude/skills`.

**For a teammate**: they need collaborator access to this private repo, then `git clone` it and run the same symlink step locally — cloning alone doesn't make the skills usable, they still need to be linked in.

## Using one

**Explicitly**, by name — type `/` plus the skill name, then the actual request:
```
/gsap-motion-timelines add a staggered entrance animation to the hero
/tailwind-utility-styling clean up this component's class list
```

**Automatically** — describe the task in plain language without naming a skill, and the matching one gets pulled in based on its description. No command needed, but less predictable when a specific skill needs testing on its own.

## Which skills, and when

Not every skill applies to every site — most are alternatives to each other or situational, not a checklist to run end to end.

**Start here, for a from-scratch build**
| Skill | For |
|---|---|
| [`site-build-kickoff`](web-design/site-build-kickoff/SKILL.md) | The entry point — walks through the brief, page inventory, and which skills below apply where, producing a plan before any screen gets generated |

**Foundation — used constantly across every page**
| Skill | For |
|---|---|
| [`spec-driven-ui-prompting`](ui/spec-driven-ui-prompting/SKILL.md) | Turning a vague ask into a brief before generating any screen |
| [`tailwind-utility-styling`](web-design/tailwind-utility-styling/SKILL.md) | Tailwind CSS conventions and pitfalls |
| [`product-motion-system`](web-design/product-motion-system/SKILL.md) | The motion vocabulary (durations, easing, primitives) everything else should follow |

**Structural — each page picks one, used once**
| Skill | For |
|---|---|
| [`single-offer-landing-page`](web-design/single-offer-landing-page/SKILL.md) | A landing/marketing page built around one action |
| [`pricing-page-design`](web-design/pricing-page-design/SKILL.md) | A pricing page |
| [`consistent-brand-logo-row`](web-design/consistent-brand-logo-row/SKILL.md) | A trust strip / "as seen in" / integrations row |
| [`progress-line-scroll-story`](web-design/progress-line-scroll-story/SKILL.md) | Turning an ordered process (onboarding, roadmap, steps) into a scroll timeline |
| [`scroll-narrative-world`](web-design/scroll-narrative-world/SKILL.md) | Turning a long-form article, case study, or brand story into one continuous scroll journey (video, 3D world, or data/type — pick one renderer) |

**Structural building blocks — nearly every full site needs these**
| Skill | For |
|---|---|
| [`responsive-nav-header`](web-design/responsive-nav-header/SKILL.md) | The primary nav header and its accessible mobile-menu collapse |
| [`site-footer-pattern`](web-design/site-footer-pattern/SKILL.md) | The footer — link groups, legal row, where a newsletter/social actually belongs |
| [`form-input-states`](web-design/form-input-states/SKILL.md) | Form fields — all visual states, validation timing, accessible error announcement |
| [`loading-empty-states`](web-design/loading-empty-states/SKILL.md) | Skeletons vs. spinners, and what a real empty state needs beyond "no data" |

**Pick ONE — competing directions, not a stack**
| Skill | Direction |
|---|---|
| [`dark-glassmorphism-panel`](web-design/dark-glassmorphism-panel/SKILL.md) + [`dark-glass-workspace-layout`](web-design/dark-glass-workspace-layout/SKILL.md) | Dark, frosted-glass, monochrome workspace feel |
| [`warm-neutral-minimal-light`](web-design/warm-neutral-minimal-light/SKILL.md) | Calm warm-beige light mode instead |
| [`bracket-frame-grid`](web-design/bracket-frame-grid/SKILL.md) | Technical/editorial framed-grid layout |
| [`full-bleed-image-grid`](web-design/full-bleed-image-grid/SKILL.md) | Cinematic image-led layout instead |

**3D / WebGL — decide the direction, then build it**
| Skill | For |
|---|---|
| [`webgl-hero-direction`](web-design/webgl-hero-direction/SKILL.md) | Deciding *whether and what kind* of WebGL hero fits the brand goal, before writing any shader code — run this first |
| [`threejs-scene-fundamentals`](web-design/threejs-scene-fundamentals/SKILL.md) | Building the scene once that decision is made — the scene/camera/renderer mental model, cleanup, and the mistakes that leak memory or melt mobile GPUs |

**Situational — used only where that specific effect is wanted**
| Skill | Effect |
|---|---|
| [`scroll-reveal-intersection-observer`](web-design/scroll-reveal-intersection-observer/SKILL.md) | Plain CSS/JS fade-in-on-scroll, no library |
| [`gsap-motion-timelines`](web-design/gsap-motion-timelines/SKILL.md) | Orchestrated sequences, scroll-driven sections (needs GSAP) |
| [`cinematic-scroll-anatomy`](web-design/cinematic-scroll-anatomy/SKILL.md) | Full page-level cinematic scroll choreography — preloader, sticky card stacks, pinned/scrubbed scenes (builds on `gsap-motion-timelines`) |
| [`word-by-word-reveal`](web-design/word-by-word-reveal/SKILL.md) | Restrained word-by-word text entrance |
| [`overflow-mask-word-reveal`](web-design/overflow-mask-word-reveal/SKILL.md) | Sharper "rising through a slit" text entrance (needs GSAP) |
| [`cursor-spotlight-reveal`](web-design/cursor-spotlight-reveal/SKILL.md) | Cursor-following before/after or x-ray image reveal |
| [`gradient-border-edge`](web-design/gradient-border-edge/SKILL.md) | A refined gradient edge instead of a flat border |
| [`edge-fade-alpha-mask`](web-design/edge-fade-alpha-mask/SKILL.md) | Soft edge fade on a marquee, scroll row, or long list |
| [`layered-progressive-blur`](web-design/layered-progressive-blur/SKILL.md) | Blur that's strong at an edge, fading to none (nav bars, docks) |
| [`layered-elevation-shadows`](web-design/layered-elevation-shadows/SKILL.md) | Refined multi-layer card elevation |

**Decorative / rare — most sites use zero of these**
| Skill | Effect |
|---|---|
| [`globe-gl-data-viz`](web-design/globe-gl-data-viz/SKILL.md) | Geographic data on a 3D globe |
| [`cobe-lightweight-globe`](web-design/cobe-lightweight-globe/SKILL.md) | A small decorative spinning globe |
| [`vantajs-animated-background`](web-design/vantajs-animated-background/SKILL.md) | Animated WebGL background (waves, fog, net) |
| [`unicorn-studio-embed`](web-design/unicorn-studio-embed/SKILL.md) | Embedding a no-code designer-made WebGL scene |
| [`matterjs-2d-physics`](web-design/matterjs-2d-physics/SKILL.md) | 2D physics — falling/draggable objects |

## A realistic build order

1. Run `site-build-kickoff` first — it walks through the brief, page inventory, and produces a plan of which skills below apply where.
2. Within that: `spec-driven-ui-prompting` locks the design brief once; pick one visual theme and one structural layout from the "pick ONE" group as the site-wide system.
3. If the brief actually calls for a WebGL/3D moment, run `webgl-hero-direction` to choose the direction before `threejs-scene-fundamentals` gets used to build it — don't reach for either by default.
4. Build each page (and the shared header/footer/forms/loading states) by describing what's needed in plain language; the matching 1–3 skills get pulled in automatically.
5. Layer in situational polish (borders, shadows, blur, cinematic scroll choreography) once structure and content are locked, not before.

## Credits

Several of these skills started as adaptations of [MengTo/Skills](https://github.com/MengTo/Skills) (MIT licensed) and were rewritten/reorganized for this library. See [NOTICE.md](NOTICE.md) for the required upstream license text.
