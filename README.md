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

**Pick ONE — competing directions, not a stack**
| Skill | Direction |
|---|---|
| [`dark-glassmorphism-panel`](web-design/dark-glassmorphism-panel/SKILL.md) + [`dark-glass-workspace-layout`](web-design/dark-glass-workspace-layout/SKILL.md) | Dark, frosted-glass, monochrome workspace feel |
| [`warm-neutral-minimal-light`](web-design/warm-neutral-minimal-light/SKILL.md) | Calm warm-beige light mode instead |
| [`bracket-frame-grid`](web-design/bracket-frame-grid/SKILL.md) | Technical/editorial framed-grid layout |
| [`full-bleed-image-grid`](web-design/full-bleed-image-grid/SKILL.md) | Cinematic image-led layout instead |

**Situational — used only where that specific effect is wanted**
| Skill | Effect |
|---|---|
| [`scroll-reveal-intersection-observer`](web-design/scroll-reveal-intersection-observer/SKILL.md) | Plain CSS/JS fade-in-on-scroll, no library |
| [`gsap-motion-timelines`](web-design/gsap-motion-timelines/SKILL.md) | Orchestrated sequences, scroll-driven sections (needs GSAP) |
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

1. Run `spec-driven-ui-prompting` once to lock the design brief (colors, type, layout language).
2. Pick one visual theme and one structural layout from the "pick ONE" groups above — that becomes the site-wide system.
3. Build each page by describing what's needed in plain language; the matching 1-3 skills get pulled in automatically.
4. Layer in polish (borders, shadows, blur) once structure and content are locked, not before.
