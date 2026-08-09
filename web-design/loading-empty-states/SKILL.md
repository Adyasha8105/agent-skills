---
name: loading-empty-states
description: Use for the states between "nothing loaded yet" and real content — skeleton screens vs. spinners, what an empty state (zero results, a fresh account, a cleared list) actually needs, and how to avoid layout shift when real content finally arrives.
---

# Loading & Empty States

## Two different problems that look similar
"Loading" (content is coming, just not here yet) and "empty" (there's genuinely nothing, and there won't be until the user does something) are different states that need different treatment. Showing a spinner forever for what's actually an empty list is a common bug that reads as "this is broken," not "there's nothing here yet."

## Choosing between a skeleton and a spinner
- **Skeleton screens** (gray placeholder blocks shaped like the real content) — use when the content's *shape* is known in advance (a card grid, a list of rows, a profile layout). The skeleton previews the real layout, so the transition from loading to loaded doesn't cause the page to visually reorganize.
- **Spinners** — use when there's no known shape to preview (a full-page transition, a background action with no associated layout, a short wait under ~1 second where a skeleton would flash too briefly to register as anything).
- Never both at once for the same content — a skeleton with a spinner overlaid on top is redundant and visually busy.

```css
.skeleton {
  background: linear-gradient(90deg, var(--sk-base) 25%, var(--sk-highlight) 50%, var(--sk-base) 75%);
  background-size: 200% 100%;
  animation: skeleton-sweep 1.5s ease-in-out infinite;
}
@keyframes skeleton-sweep {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
@media (prefers-reduced-motion: reduce) {
  .skeleton { animation: none; background: var(--sk-base); }
}
```

## Avoiding layout shift when content arrives
The skeleton (or the container it sits in) needs to reserve the *same* dimensions the real content will occupy — a skeleton card that's 200px tall replaced by a real card that's 340px tall once an image loads causes everything below it to jump. Set explicit `width`/`height` (or `aspect-ratio` for images) on the container up front, sized from the real content's known dimensions, not from the skeleton's convenient placeholder size.

## What a real empty state needs
An empty state is not just "hide the list and show nothing." It needs, at minimum:
- A short, specific message — "No orders yet" reads very differently from a generic "No data," and specificity is what makes it feel intentional rather than like an error.
- A clear next action, when one exists — a button or link that resolves the emptiness (e.g. "Browse recipes" on an empty saved-items list), not just a passive description of the absence.
- Visual weight appropriate to context — a small inline empty state for one section of a busy dashboard; a fuller illustrated state for a page that's *entirely* empty (a brand-new account's first screen).

Distinguish, in the copy and the action offered, between these genuinely different empty states:
- **Zero results from a search/filter** — the action is usually "clear filters," not "create new."
- **Genuinely nothing yet** (new account, first use) — the action is usually "create/add your first X," often paired with a short explainer of what will appear here once something exists.
- **Everything was intentionally cleared/completed** (an empty inbox, a completed task list) — this one can actually feel positive; don't reuse the same "nothing here" tone that a zero-results state uses.

## Where this goes wrong
- A spinner that never resolves because the underlying state is actually "empty," not "still loading" — the two need to be distinguished in code, not just visually.
- Skeleton dimensions that don't match the real content's eventual size, causing a layout jump the moment real content swaps in.
- An empty state that's just blank space with no message at all — reads as broken, not as "nothing here yet."
- Reusing one generic "No data" empty state across zero-results, first-use, and intentionally-cleared cases that actually call for different messages and different (or absent) actions.

## Before building, confirm
- Whether the content's shape is knowable in advance (→ skeleton) or not (→ spinner), and how long the wait typically is (a skeleton for a sub-200ms load will flash uselessly).
- What the real content's typical dimensions are, so the loading placeholder can reserve that space instead of guessing.
- Which specific empty-state flavor applies here — zero-results, first-use, or intentionally-cleared — since each wants different copy and a different (or no) call to action.
