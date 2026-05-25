---
name: a11y-bar
description: Accessibility standards (WCAG 2.1 AA) for this project. Use whenever building, modifying, or reviewing UI — adding interactive elements, forms, images, modals, navigation, or animations. Covers semantic HTML, keyboard navigation, ARIA usage, focus management, color contrast, and motion preferences. Apply this skill any time UI code is involved, even if the user doesn't explicitly mention accessibility.
---

# Accessibility Bar

This project holds to **WCAG 2.1 AA** as the minimum. Accessibility is not a "polish later" item — it's part of the definition of done.

## Core principle: semantic HTML first

Reach for ARIA only when semantic HTML can't express what you need. The right element solves most problems automatically.

| Use this | Not this |
|---|---|
| `<button>` | `<div onClick>` or `<span onClick>` |
| `<a href="...">` | `<button>` for navigation |
| `<label for="...">` | `<div>` with text near an input |
| `<nav>`, `<main>`, `<aside>`, `<header>`, `<footer>` | `<div className="nav">` |
| `<h1>`–`<h6>` in order | Skipping levels for visual size |
| `<ul>`/`<ol>` for lists | `<div>` repeated with item styles |
| `<table>` for tabular data | `<div>` grid for tables |
| `<dialog>` or proper modal | `<div>` overlay without ARIA |

## Interactive elements

### Every interactive element must be:

1. **Reachable by keyboard** — Tab order must work. No `tabIndex={-1}` on things users need to reach.
2. **Activatable by keyboard** — `<button>` and `<a>` get this free. Custom interactive elements need `onKeyDown` for Space/Enter.
3. **Have a visible focus state** — never disable focus outlines without replacing them. `outline-none focus:ring-2 focus:ring-offset-2` is a common pattern.
4. **Have an accessible name** — visible text, `aria-label`, or `aria-labelledby`.

```tsx
// ❌ Bad — div with onClick
<div className="cursor-pointer" onClick={handleClick}>Submit</div>

// ✅ Good — semantic button
<button type="button" onClick={handleClick}>Submit</button>

// ✅ If you must use a div (rarely justified)
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      handleClick();
    }
  }}
  aria-label="Submit"
>
  Submit
</div>
```

### Icon-only buttons

Always need an accessible name:

```tsx
<button type="button" aria-label="Close dialog">
  <XIcon aria-hidden="true" />
</button>
```

The icon itself gets `aria-hidden="true"` so screen readers don't announce it as well as the label.

## Forms

### Labels

Every input has an associated label. Three valid patterns:

```tsx
// 1. Explicit association (preferred)
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// 2. Wrapped (works without id)
<label>
  Email
  <input type="email" />
</label>

// 3. aria-label when visual label isn't possible (e.g. search in a toolbar)
<input type="search" aria-label="Search" placeholder="Search..." />
```

**Placeholder is not a label.** It disappears when the user types. Always pair with a real label.

### Required fields

```tsx
<label htmlFor="email">
  Email <span aria-hidden="true">*</span>
</label>
<input id="email" type="email" required aria-required="true" />
```

### Error messages

Associate errors with the input via `aria-describedby`:

```tsx
<label htmlFor="email">Email</label>
<input
  id="email"
  type="email"
  aria-invalid={hasError}
  aria-describedby={hasError ? 'email-error' : undefined}
/>
{hasError && (
  <p id="email-error" role="alert">
    Please enter a valid email address.
  </p>
)}
```

### Fieldsets for groups

Related inputs (radio groups, checkbox groups) belong in a `<fieldset>` with `<legend>`:

```tsx
<fieldset>
  <legend>Notification preferences</legend>
  <label><input type="checkbox" name="prefs" value="email" /> Email</label>
  <label><input type="checkbox" name="prefs" value="sms" /> SMS</label>
</fieldset>
```

## Images and media

| Image type | Alt text |
|---|---|
| Informative (conveys content) | Describe the content: `alt="Bar chart showing Q3 revenue up 12%"` |
| Decorative (purely visual) | Empty: `alt=""` (still required — never omit the attribute) |
| Functional (in a link/button) | Describe the action: `alt="Open menu"` |
| Complex (chart, infographic) | Brief alt + longer description elsewhere |

```tsx
// ❌ Bad
<img src="/team-photo.jpg" />

// ✅ Decorative
<img src="/decoration.svg" alt="" />

// ✅ Informative
<img src="/team-photo.jpg" alt="The engineering team at the 2024 offsite" />
```

### Video and audio

- `<video>` needs captions (`<track kind="captions">`) and visible controls.
- `<audio>` needs a transcript available.
- Auto-playing media with sound is a violation — never enable autoplay with audio.

## Headings

- **One `<h1>` per page** (or per main content region).
- **Don't skip levels.** `<h2>` follows `<h1>`, `<h3>` follows `<h2>`. Don't jump from `<h2>` to `<h4>` for visual styling.
- **Use heading level for hierarchy, not size.** Size is what Tailwind classes are for. A small heading can still be `<h2>`.

## ARIA — use sparingly, correctly

### Common correct uses

- `aria-label` / `aria-labelledby` — accessible names when visible text isn't available
- `aria-describedby` — extra description (form errors, hints)
- `aria-expanded` — for disclosure widgets (accordions, dropdowns)
- `aria-current="page"` — for the active item in navigation
- `aria-hidden="true"` — for decorative icons next to a labeled element
- `role="alert"` — for live error messages
- `role="status"` — for polite status updates (loading, saved)

### Common mistakes

- ❌ `aria-label` on an element with visible text — duplicates the label
- ❌ `aria-hidden="true"` on a focusable element — creates a focus trap
- ❌ `role="button"` on an actual `<button>` — redundant, can cause issues
- ❌ Made-up roles or attributes — only use spec-defined ones

## Focus management

### Modals and dialogs

- **Focus moves into the modal** when it opens (usually to the first focusable element or the close button).
- **Focus is trapped** inside the modal while it's open.
- **Focus returns** to the element that opened the modal when it closes.
- **Escape closes** the modal.

Use the native `<dialog>` element or a well-tested library — implementing this manually is error-prone.

### Skip links

For pages with significant navigation, provide a "Skip to main content" link as the first focusable element. Hide it visually until focused.

### Don't steal focus

Don't programmatically move focus when the user hasn't initiated an action. Auto-focusing an input on page load is sometimes OK (login forms), often disorienting (random search bars).

## Color and contrast

WCAG AA minimums:

| Content | Contrast ratio |
|---|---|
| Body text (under 18pt regular / 14pt bold) | **4.5:1** |
| Large text (18pt+ regular / 14pt+ bold) | **3:1** |
| UI components and graphics | **3:1** |

**Don't rely on color alone** to convey information. A red error should also have an icon or text label. A "required" indicator shouldn't just be a red asterisk — the `*` itself does the job for sighted users; pair with `aria-required` for assistive tech.

## Motion

Respect `prefers-reduced-motion`:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

Or in Tailwind:

```tsx
<div className="transition-transform motion-reduce:transition-none">
```

Avoid:
- Parallax scrolling without an off switch
- Auto-playing animations longer than 5 seconds without pause controls
- Flashing content (more than 3 flashes per second is a seizure risk)

## Quick audit checklist

When reviewing UI, mentally walk through:

1. **Tab through it.** Can I reach every interactive element? Is the order logical?
2. **Use only the keyboard.** Can I activate everything?
3. **Look at focus rings.** Are they visible? Do they meet contrast requirements?
4. **Read the markup.** Are the right elements being used (`<button>` for actions, `<a>` for navigation)?
5. **Check labels.** Does every input have one? Are icons-only buttons labeled?
6. **Check contrast.** Body text 4.5:1, large text 3:1?
7. **Check images.** Do they have appropriate `alt` text?
8. **Toggle reduced motion** in the OS. Do animations respect it?

If anything fails, it's not done.
