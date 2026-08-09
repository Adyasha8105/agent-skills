---
name: pricing-page-design
description: Use when designing or rewriting a pricing page. The job isn't to show prices — it's to reduce uncertainty enough that a visitor can choose confidently. Covers discovery questions, plan-table structure, layout shape selection, objection-handling FAQ topics, and copy patterns specific to pricing (distinct from a general landing page).
---

# Pricing Page Design

The framing that matters most: a pricing page's job is not to display numbers, it's to remove enough uncertainty that choosing feels safe. Every design and copy decision should be judged against "does this make the choice easier," not "does this look complete."

## Discover before designing
**The offer and its value metric** — what's being sold, who it's for, and critically, what actually scales the price (seats, usage volume, projects, revenue)? Getting the value metric wrong undermines the whole page, since every plan's story is built around it.

**The plans themselves** — names, prices (monthly and annual), the 3–6 limits per plan that actually matter to a buyer, and specifically what triggers someone to upgrade from one tier to the next. That upgrade trigger is what plan copy should be selling, more than the plan itself.

**Objections and risk** — the top few reasons people hesitate, any compliance/security requirements a B2B buyer will ask about (SOC2, GDPR, etc.), and what's available to de-risk the decision (trial, free tier, money-back, a demo).

**Where traffic is coming from** — homepage, a feature page, an ad, a comparison page — since that determines how much context a visitor already has before they land here.

## The structural skeleton
Above the fold: a headline stating the value (not "Pricing" as a headline), a monthly/annual toggle with the annual savings called out, the pricing table itself (3 plans is the common case, 2 for a genuinely simple product), and one consistent CTA verb per plan.

Below the fold: a readable plan-comparison (grouped by category — Core, Collaboration, Admin/Security, Support — never a giant undifferentiated spreadsheet), an FAQ built around real objections, proof placed near the actual decision point rather than in a separate testimonials section, a compliance/procurement section if this is B2B, and a final CTA plus a path to talk to sales.

## Picking a layout shape
- **Classic three-card** — the default when there are three natural tiers and pricing itself is simple. Exactly one plan gets a "Recommended" treatment, understated rather than shouted.
- **Value-metric slider** — when price genuinely scales with usage (seats, events, credits). The math has to stay obvious at a glance, and the slider's default position should land on a realistic median customer, not an extreme.
- **Two-path split** — when the audience itself splits cleanly (individuals vs. teams). Separate by *who they are* first, then show price — reversing that order confuses more than it clarifies.
- **Enterprise as the last mile** — when there's both a self-serve path and a sales-led one. The enterprise tier should read as procurement reassurance (security, SLAs, dedicated support) more than as a fourth price point.

## What actually moves the decision
- Cap it at three plans unless there's a real reason for more — every additional plan is a chance for analysis paralysis, not a chance to capture more revenue.
- Exactly one plan recommended, never a tie between two.
- Every bullet describes an outcome the buyer gets, not an internal feature name — "ship unlimited client sites without extra fees," not "unlimited projects."
- Anchor annual pricing honestly (a real "save X%" callout) rather than manufacturing urgency with fake scarcity or hidden fees that surface later.
- Every plan needs at least one de-risking element attached — a trial, a free tier, a guarantee, or (for "Contact sales") a concrete promise like response time or what a demo actually covers.

## Objection-handling FAQ topics that consistently matter
Cancellation terms, what happens at a usage/seat limit, discount availability, who this fits (freelancer vs. team), and security/data/compliance. Write these as genuine question-and-answer pairs — that phrasing doubles as groundwork for AI answer engines surfacing the page, not just traditional search.

## Copy patterns
Headline shapes: *"{outcome} for {audience}, without {the usual pain}"* · *"Plans that scale from {small} to {big}"* · *"Start small, upgrade when {the actual trigger}."*

Plan descriptions in two lines: who it's for, then what it unlocks — e.g. **Pro** — for people shipping every week; faster iteration, better components.

CTA verbs should match the actual action and stay identical in *style* across every plan (all verb-led, e.g. "Start free trial" / "Buy Pro" / "Contact sales") — mixing "Get started" on one plan and "Try now" on another reads as inconsistent even when both are individually fine.

## Pricing table UI checklist
Visible monthly/annual toggle with a real savings callout · recommended-plan styling that's noticeable but not loud · 3–6 limits and 3–6 included items visible per plan, not an exhaustive list · one unambiguous next step under every plan · a "compare plans" link that jumps to the full matrix · and on mobile, the table collapses to stacked cards, never a horizontal-scroll table that's painful to navigate on a small screen.

## SEO and answer-engine basics
Title along the lines of "Pricing — {Product}" plus an outcome keyword; a meta description with one sentence of value and one sentence naming the starting price; a clean `/pricing` URL; internal links in from the homepage CTA, feature pages, and any comparison pages. The FAQ section doubles as AEO groundwork when written as plain Q&A, optionally backed by FAQ schema if the stack supports it.

## Where pricing pages usually fail
Too many plans causing analysis paralysis. Feature names listed with no stated reason to care. Every plan hidden behind "Contact sales," which defeats the point of having a self-serve pricing page at all. The value metric quietly changing partway down the page. An over-designed comparison table that trades readability for visual flourish.

## What to hand back when generating one
Page outline → pricing table spec (plans, bullets, limits, CTA per plan) → FAQ list (6–12 pairs) → SEO/AEO notes (title, meta, FAQ-schema suggestion) → which of the four layout shapes, and why.
