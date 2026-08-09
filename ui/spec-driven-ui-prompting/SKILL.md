---
name: spec-driven-ui-prompting
description: Use before generating any UI screen, component, or visual asset from a prompt. Turns a vague ask into an explicit design brief covering layout, type, color, and constraints, so output stays consistent across iterations instead of drifting each time.
---

# Spec-Driven UI Prompting

Treat every UI generation prompt as a short design brief, not a description of a feeling. A model given "make it look premium" will guess differently every time; a model given exact hierarchy, spacing, and color rules will reproduce the same direction across variants.

## Write the brief in five parts

**Intent** — what screen or component this is, who sees it, and what it needs to accomplish in a glance versus a slow read.

**Structure** — the grid, the placement of the dominant element, and the reading order (headline → support line → action).

**Type** — the actual weight and size relationship between levels, not just a font name. "H1 heavy at 2x body size, body regular and loose" travels better than "clean sans-serif."

**Color** — background, text color, and exactly one accent. Naming two accents is how output turns into a rainbow.

**Boundaries** — what must stay fixed between variants, and what must never appear at all (no placeholder logos, no invented copy, no decorative gibberish text).

## Change one thing per iteration

Once a first pass nails structure, type, and color, don't regenerate from scratch to explore a variant — hold everything fixed and swap exactly one axis: accent hue, crop, card count, or background tone. Regenerating everything at once makes it impossible to tell which change caused which improvement.

## Don't fight the model on text rendering

Models render typography unreliably. Rather than iterating endlessly on a misspelled headline, split the work: generate the visual composition with an empty text-safe zone reserved, then place real type afterward in a design tool.

## Anchor style with a spec card, not adjectives

A short block of explicit key/value pairs reads as more binding than prose:

```
font: Canela
mode: dark
style: minimal
```

This works because it looks like a constraint rather than a suggestion — models tend to treat explicit values as harder rules than adjectives buried in a sentence.

## Keep a reference folder instead of relying on memory

If a visual direction needs to persist across many prompts in a session, save example images locally and point each prompt at that folder instead of re-describing the aesthetic from scratch every time.

## When the request is vague, ask before generating

- What's the one thing this screen needs to communicate?
- What's the read order — headline, support line, then what action?
- Which visual lane: minimal, glass/blur, editorial, or illustrated?
- Is anything already fixed — an existing type system, a locked color, a grid?
