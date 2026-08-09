---
name: responsive-nav-header
description: Use when building a site's primary navigation header — logo, links, primary action, and how it collapses to a mobile menu. Covers the structural anatomy, the mobile hamburger overlay done accessibly, and scroll-triggered style changes.
---

# Responsive Nav Header

## The anatomy, before any styling
Almost every header is three zones: a logo/brand mark (left), primary links (center or left-adjacent), and a primary action or account control (right). Decide what's actually essential in each zone before designing any of them — a header crowded with six nav links plus a search box plus a CTA plus an account menu has no zone left for hierarchy.

```html
<header class="site-header">
  <a class="site-header__logo" href="/">Brand</a>
  <nav class="site-header__links" aria-label="Primary">
    <a href="/features">Features</a>
    <a href="/pricing">Pricing</a>
  </nav>
  <div class="site-header__actions">
    <a class="site-header__cta" href="/signup">Get started</a>
    <button class="site-header__menu-toggle" aria-expanded="false" aria-controls="mobile-menu">Menu</button>
  </div>
</header>
```

## The mobile collapse, done accessibly
Below the breakpoint where links stop fitting, the `nav` collapses behind a toggle button, not into an overflowing scrollable row of tiny links. The toggle button itself needs real accessible wiring, not just a visual hamburger icon:
- `aria-expanded` on the button reflects open/closed state, toggled in the same handler that shows/hides the menu.
- `aria-controls` points at the menu's `id`, so assistive tech knows what the button opens.
- When the menu opens, focus should move into it (typically to the first link or a close button) — otherwise a screen reader user "opens" a menu that focus never actually entered.
- When the menu closes (via toggle, `Escape`, or clicking outside it), focus should return to the toggle button — losing focus back to the top of the document on close is disorienting.
- While open, the menu should trap focus (Tab shouldn't escape it into content hidden behind an overlay) and background scroll should be locked.

```js
function toggleMobileMenu(button, menu) {
  const isOpen = button.getAttribute("aria-expanded") === "true"
  button.setAttribute("aria-expanded", String(!isOpen))
  menu.hidden = isOpen
  if (!isOpen) {
    menu.querySelector("a, button")?.focus()
    document.body.style.overflow = "hidden"
  } else {
    button.focus()
    document.body.style.overflow = ""
  }
}
```

## Scroll-triggered style changes
A header that starts transparent over a hero image and becomes solid once scrolled past it is a common pattern — implement it the same way as any other scroll-driven visual state: an `IntersectionObserver` watching a sentinel element at the bottom of the hero, toggling a class on the header when that sentinel leaves the viewport, rather than reading `window.scrollY` on every scroll event.
```js
const sentinel = document.querySelector("#hero-end")
const header = document.querySelector(".site-header")
new IntersectionObserver(([entry]) => {
  header.classList.toggle("site-header--solid", !entry.isIntersecting)
}).observe(sentinel)
```

## Active-link and sticky behavior
- Mark the current page's link with `aria-current="page"`, styled distinctly — don't rely on color alone to signal which link is active.
- A `position: sticky` header needs `z-index` coordination with everything else that might scroll underneath it (hero overlays, modals) — decide the header's place in the stacking order up front rather than discovering a conflict later.
- If the header is sticky, account for its height with `scroll-margin-top` on anchor targets elsewhere on the page, or an in-page jump lands content behind the header.

## Where this breaks
- Toggle button with no `aria-expanded`/`aria-controls` — visually works, invisible to assistive tech.
- Focus not moving into the opened mobile menu, or not returning to the toggle on close.
- Background content still scrollable (and focusable) behind an open mobile overlay.
- Reading `scrollY` in a raw unthrottled scroll listener for the solid/transparent transition, causing jank on scroll instead of a clean threshold-based toggle.

## Before building, confirm
- What actually belongs in each of the three zones — resist adding "just one more" link or control.
- Whether the header needs to change appearance on scroll (transparent-over-hero → solid), or stays one consistent style throughout.
- Whether it should be sticky, and if so, what else on the page needs `z-index`/`scroll-margin` coordination as a result.
