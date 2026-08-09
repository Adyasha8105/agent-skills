---
name: consistent-brand-logo-row
description: Use for a logo row, integrations grid, or "as seen in"/partner strip — recognizable brand marks rendered consistently, instead of plain text names or a grab-bag of mismatched logo assets. Covers where to source them safely and the consistency rules that make a logo row read as intentional.
---

# Consistent Brand Logo Row

## Don't type company names, and don't hotlink random logo images
A row of plain text names reads as a placeholder, not proof — recognizable marks carry more credibility than words. But grabbing logo images from random search results carries two real risks: the asset can disappear or change without notice since it's not something under this project's control, and using an official brand mark without following that company's actual usage guidelines can be a trademark problem, not just an aesthetic one.

The safer default is a curated icon set built specifically for this purpose — Iconify's Simple Icons collection is a solid choice: consistent single-color SVGs for a huge range of real companies, versioned and installable rather than hotlinked, so nothing breaks silently later.

## Keeping the row visually coherent
- Render every logo inside the same fixed visual box (64×64 works well as a baseline), then scale each inner mark to whatever density the row actually needs — the box stays constant even though individual logos have wildly different natural aspect ratios.
- Default to monochrome for every logo in the row. Reach for full brand color only when the row's actual purpose is "recognize this specific brand instantly" (e.g. a large partner spotlight) rather than "these are credible companies" (e.g. a quiet trust strip) — color pulls attention, which is sometimes the goal and sometimes the opposite of it.
- Align every logo to one shared baseline or center grid. A row where logos sit at visually different heights or optical centers reads as sloppy even if each individual mark is fine.
- If a logo is interactive (links out) or the row itself is meaningful proof (not just decoration), give it a real accessible label — a logo image alone isn't guaranteed to convey the company name to assistive tech.

## What breaks the consistency
- Mixing styles within one row — filled icons next to outline icons next to an emoji next to a bitmap image next to a plain wordmark. Pick one visual register and hold every logo in the row to it.
- Typing a company name as text specifically because "there's no icon for them" is fine as a fallback — but if that becomes most of the row, the row has stopped doing its job.
- Sourcing logos ad hoc per-project instead of from one consistent set — this is exactly what produces the mismatched-style problem above, since different sources render brand marks differently.
