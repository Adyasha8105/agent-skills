---
name: form-input-states
description: Use when building form fields — the label/input/help-text/error anatomy, the full set of visual states (default, focus, error, disabled, success), when validation should actually fire, and how to announce an error accessibly instead of just coloring the border red.
---

# Form Input States

## The anatomy, before any visual state
Every field is four parts, and each needs to exist even when visually minimal:
```html
<div class="field">
  <label for="email">Email</label>
  <input id="email" type="email" aria-describedby="email-help email-error" />
  <p id="email-help" class="field__help">We'll only use this to send order updates.</p>
  <p id="email-error" class="field__error" role="alert" hidden>Enter a valid email address.</p>
</div>
```
`aria-describedby` links the input to *both* the help text and the (currently hidden) error text — assistive tech reads whichever is actually present, so both ids stay wired up permanently rather than being added only once an error exists.

## The full state set
A field isn't just "normal" and "error" — plan for all of these before writing CSS, not while debugging why one looks wrong:
- **Default** — resting state.
- **Focus** — needs a visible focus ring that survives regardless of the rest of the visual theme; never remove the browser default without replacing it with something equally visible.
- **Filled** — sometimes styled distinctly from empty-default, mostly relevant for floating-label patterns.
- **Error** — border/text color change *plus* the error message actually rendering, not color alone (color-only error signaling fails for colorblind users and fails entirely for screen readers).
- **Disabled** — visually muted, and also actually non-interactive (`disabled` attribute, not just a CSS style pretending it's disabled while it's still focusable and submittable).
- **Success/valid** — optional, but useful for fields validated as the user progresses through a multi-field form (e.g. a username-availability check).

## When validation should actually fire
- **On blur**, for most fields — validating on every keystroke while a user is still mid-typing an email address flags "invalid" on a string that's simply incomplete, not wrong.
- **On submit**, always, as a final gate — even if every field validated individually, re-check on submit in case fields interact (password + confirm-password, date-range start/end).
- **As-you-type**, only for the specific cases where immediate feedback is the point — a password-strength meter, a username-availability check with debounced input. This should be the exception, not the default validation timing.

## Announcing an error accessibly
Toggling `hidden` on the error text isn't enough on its own for a screen reader to notice it appeared, since visibility changes to existing DOM aren't automatically announced. `role="alert"` on the error element (as in the markup above) means content appearing inside it gets announced when it appears — that's what makes the error actually heard, not just visible.
```js
function showError(field, message) {
  const errorEl = document.getElementById(`${field.id}-error`)
  errorEl.textContent = message
  errorEl.hidden = false
  field.setAttribute("aria-invalid", "true")
}
function clearError(field) {
  const errorEl = document.getElementById(`${field.id}-error`)
  errorEl.hidden = true
  field.removeAttribute("aria-invalid")
}
```
`aria-invalid="true"` on the input itself is a second, independent signal — some assistive tech announces it on focus even before reaching the error text.

## Where this breaks
- Color-only error indication with no text message — invisible to screen readers, ambiguous for colorblind users.
- Validating on every keystroke, producing a field that flashes "invalid" while someone is still in the middle of typing a correct value.
- A `disabled`-looking button that's actually still clickable (styled to look disabled without the real attribute), or a disabled input that's still focusable and included in form submission.
- Error text present in the DOM but not wired to the input via `aria-describedby` — a sighted user sees it, a screen reader user doesn't necessarily connect it to the right field.
- Removing the default focus outline for aesthetic reasons without providing an equally visible custom one — this is one of the most common real accessibility regressions in polished UI.

## Before building, confirm
- Which fields need on-submit-only validation versus genuine as-you-type feedback (and why that field is the exception).
- Whether any field has cross-field validation (password confirmation, date ranges) that needs to run at submit regardless of individual field state.
- What the disabled state actually communicates here — "not available yet" vs. "already filled in and locked" read very differently and may need different visual treatment.
