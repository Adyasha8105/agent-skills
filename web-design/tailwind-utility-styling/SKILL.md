---
name: tailwind-utility-styling
description: Use when building or styling UI with Tailwind CSS — layout, responsive behavior, state variants, dark mode, and keeping class lists from becoming unreadable. Framework-agnostic (React, Vue, Svelte, plain HTML).
---

# Tailwind Utility Styling

## Why utility-first works
Composing spacing, color, and typography directly in markup skips the round-trip of naming a CSS class and hunting down its definition later. The tradeoff is verbosity in the markup itself — worth it once you have a system in place to tame that verbosity.

## The variant vocabulary
- Breakpoints stack left-to-right and apply upward: `md:` means "at medium and above."
- State variants chain onto any utility: `hover:`, `focus-visible:`, `disabled:`, `group-hover:`, `peer-invalid:`.
- Arbitrary values (`w-[42rem]`, `bg-[#0b1220]`) are an escape hatch, not a default — reach for a value from the scale first.
- Dark mode via the `dark:` prefix assumes a class-based toggle strategy (`class="dark"` on `<html>`), not the OS media query, if a manual switch is wanted.

## Taming the class list
1. First line of defense: extract a component (in whatever framework is in use), not a CSS class.
2. Second line: `@apply` for a genuinely repeated cluster of utilities — sparingly, since every `@apply` block is a small step back toward hand-written CSS.
3. For long class strings, prefer a small array/template composition (or `clsx`/`cva`) over one 300-character `class=""` blob.

## The build-time trap
Tailwind's compiler only emits CSS for class names it can find as literal strings in the source. This breaks silently for:
```js
// Won't be picked up — the compiler can't see "danger" as a full class name
const cls = `text-${color}-600`
```
Fix it with an explicit lookup table instead:
```js
const toneClass = {
  success: "bg-emerald-600",
  danger: "bg-rose-600",
  info: "bg-sky-600",
}[tone]
```
The same failure mode applies to CMS-driven or user-generated class names — anything not visible as a literal string at build time gets silently dropped, not errored on.

## Two starting patterns

**A primary action button**, with focus and active states covered so keyboard users aren't left without feedback:
```html
<button class="inline-flex items-center justify-center rounded-xl px-5 py-3
               bg-teal-600 text-white font-medium
               hover:bg-teal-500 active:bg-teal-700
               focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-teal-400/60">
  Continue
</button>
```

**A two-column hero** that collapses to a single column below the `lg` breakpoint:
```html
<section class="mx-auto max-w-6xl px-6 py-16">
  <div class="grid gap-10 lg:grid-cols-2 lg:items-center">
    <div>
      <h1 class="text-4xl font-semibold tracking-tight sm:text-5xl">
        Plan the week without the spreadsheet.
      </h1>
      <p class="mt-4 text-zinc-600">
        One line of supporting copy, no more.
      </p>
    </div>
    <div class="rounded-2xl border border-zinc-200 bg-white p-6 shadow-sm">
      <!-- media -->
    </div>
  </div>
</section>
```

## Before starting, check
- Which framework/build tool is wiring this up (Next, Vite, SvelteKit, plain HTML)? Determines where the Tailwind content-scan paths need to point.
- Is this a one-off page, or does it need to hold up as a design system (tokens, shared components)?
- Any dark mode, RTL, or accessibility constraints already decided?
