---
name: site-footer-pattern
description: Use when building a site's footer — link columns, legal/copyright row, and where a newsletter signup or social links actually belong. Covers structure, responsive stacking, and the common mistake of treating the footer as a dumping ground.
---

# Site Footer Pattern

## What a footer is actually for
A footer is the last thing on every page, so it's the default place secondary navigation ends up — but "secondary" doesn't mean "everything that didn't fit elsewhere." A footer with thirty links in six unlabeled columns serves no one; a footer organized into three or four clearly labeled groups serves as a real secondary nav.

## The structure
```html
<footer class="site-footer">
  <div class="site-footer__columns">
    <div class="site-footer__col">
      <h3>Product</h3>
      <a href="/features">Features</a>
      <a href="/pricing">Pricing</a>
    </div>
    <div class="site-footer__col">
      <h3>Company</h3>
      <a href="/about">About</a>
      <a href="/careers">Careers</a>
    </div>
    <div class="site-footer__col">
      <h3>Legal</h3>
      <a href="/privacy">Privacy</a>
      <a href="/terms">Terms</a>
    </div>
  </div>
  <div class="site-footer__bottom">
    <p>© 2026 Brand. All rights reserved.</p>
    <div class="site-footer__social">
      <a href="..." aria-label="Twitter">...</a>
    </div>
  </div>
</footer>
```
Each column gets a real heading (`<h3>` or similar), even if visually understated — a sighted user scans column headers to find a link fast, and a screen reader user relies on those same headings to skip between groups instead of hearing every link read linearly.

## Where things that "don't fit anywhere" actually belong
- **Newsletter signup** — belongs in the footer only as a low-commitment afterthought (a single email field, no multi-step form). If the newsletter is a real acquisition channel, it deserves its own section higher on the page, not a cramped footer corner.
- **Social links** — icon row in the bottom bar, grouped with copyright, not mixed into the main link columns where they compete with actual navigation.
- **Legal links** (privacy, terms, cookie policy) — their own labeled column or a plain row at the very bottom; never scattered across other columns where they look like product features.
- **Language/region switcher**, if needed — bottom bar, near copyright, since it's a rarely-used utility control rather than primary navigation.

## Responsive behavior
- On mobile, columns stack vertically — but stacking four columns of ungrouped links produces one very long scroll of text. Collapsing each column into an accordion (tap a heading to expand its links) keeps the footer scannable on a small screen without losing any content.
- The bottom bar (copyright + social + legal) typically stacks to centered, single-column on mobile regardless of how the main columns behave.

## Where this goes wrong
- Treating the footer as a dumping ground for every link that didn't have an obvious home elsewhere — if a link genuinely matters, it likely belongs in the header or a page, not buried in column four of the footer.
- Column headings styled so subtly they're not recognizable as headings at all, losing the scannability they exist to provide.
- A newsletter form with several fields and a long headline, effectively becoming a second hero section stuffed into the footer.
- Icon-only social links with no `aria-label` — a screen reader announces "link" with no indication of which platform it goes to.

## Before building, confirm
- What actually needs a link here versus what belongs in the header or on its own page.
- Whether a newsletter signup is wanted, and if so, whether it's a real acquisition feature (deserves its own section) or a low-key afterthought (fits the footer).
- How many real link groups exist — collapsing to fewer, clearly labeled columns almost always reads better than more, thinner ones.
