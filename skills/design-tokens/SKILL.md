---
name: design-tokens
description: Catalog of this project's actual design tokens — colors, spacing, fonts, breakpoints, radii, shadows. Use whenever choosing a value for any style decision, deciding whether a value exists or needs to be added, or auditing whether existing code uses the right tokens. Apply this skill any time UI styling values are being chosen, even if the user doesn't explicitly mention tokens.
---

# Design Tokens

This skill is the inventory of the actual design tokens in this project. **Keep it in sync with `tailwind.config.{js,ts}` and any CSS custom property files** (`globals.css`, `app.css`, etc.). When tokens are added or changed in the config, update this file too.

> **First-time setup**: When you first use this project, run through the project's `tailwind.config.{js,ts}` and fill in the sections below with the actual values. The placeholders are examples — replace them with what's really there. If a category isn't extended in the config, note that the project uses Tailwind defaults.

## How to use this skill

1. **Before writing any styling**, check this catalog for the value you need.
2. **If the value exists**, use the documented token name.
3. **If it doesn't exist**, refer to the `tailwind-conventions` skill for the ask-before-extending workflow.
4. **After tokens are added to the config**, update this skill so it stays accurate.

---

## Colors

> *Replace with actual project colors. Group semantically.*

### Brand

| Token | Value | Usage |
|---|---|---|
| `brand-primary` | `#________` | Primary CTAs, links, key UI accents |
| `brand-secondary` | `#________` | Secondary actions, highlights |

### Surface

| Token | Value | Usage |
|---|---|---|
| `surface` | `#________` | Default page/card background |
| `surface-elevated` | `#________` | Cards, modals, popovers |
| `surface-sunken` | `#________` | Form inputs, nested areas |

### Content (text)

| Token | Value | Usage |
|---|---|---|
| `content-primary` | `#________` | Body text, headings |
| `content-secondary` | `#________` | Supporting text, captions |
| `content-tertiary` | `#________` | Placeholders, disabled text |

### Status

| Token | Value | Usage |
|---|---|---|
| `success` | `#________` | Confirmations, success states |
| `warning` | `#________` | Warnings, cautionary states |
| `danger` | `#________` | Errors, destructive actions |
| `info` | `#________` | Informational callouts |

### Palette (raw)

If the project also exports raw palette values, document them separately. Prefer semantic names in UI code.

---

## Spacing

> *Document any extensions beyond Tailwind defaults.*

Tailwind's default spacing scale is 4px-based: `1` = 4px, `2` = 8px, `3` = 12px, `4` = 16px, etc.

### Custom additions

| Token | Value | Notes |
|---|---|---|
| `_____` | `_____` | _____ |

If the project only uses defaults, write: "Uses Tailwind default spacing scale. No extensions."

---

## Typography

### Font families

| Token | Stack | Usage |
|---|---|---|
| `font-sans` | `_____` | Default body / UI |
| `font-display` | `_____` | Headings, hero text (if used) |
| `font-mono` | `_____` | Code blocks, technical readouts |

### Font sizes

If the project extends `theme.fontSize`, document the additions here. Otherwise, Tailwind defaults apply:

- `text-xs` (12px), `text-sm` (14px), `text-base` (16px), `text-lg` (18px), `text-xl` (20px), `text-2xl` (24px), `text-3xl` (30px), `text-4xl` (36px)

### Font weights

Standard Tailwind: `font-thin` (100) → `font-black` (900) in 100 increments.

---

## Breakpoints

> *Tailwind defaults unless extended.*

| Token | Min width | Typical device |
|---|---|---|
| (base) | 0 | Mobile |
| `sm:` | 640px | Large mobile / small tablet |
| `md:` | 768px | Tablet |
| `lg:` | 1024px | Laptop |
| `xl:` | 1280px | Desktop |
| `2xl:` | 1536px | Large desktop |

Custom breakpoints (if any):

| Token | Min width | Usage |
|---|---|---|
| `_____` | `_____` | _____ |

---

## Border radius

> *Document extensions.*

Tailwind defaults: `rounded-none`, `rounded-sm` (2px), `rounded` (4px), `rounded-md` (6px), `rounded-lg` (8px), `rounded-xl` (12px), `rounded-2xl` (16px), `rounded-3xl` (24px), `rounded-full`.

Custom additions:

| Token | Value | Usage |
|---|---|---|
| `_____` | `_____` | _____ |

---

## Shadows

> *Document the design system's shadow scale. UI shadows should always come from this list.*

| Token | Usage |
|---|---|
| `shadow-sm` | Subtle (cards at rest) |
| `shadow` | Default |
| `shadow-md` | Hover states, raised elements |
| `shadow-lg` | Modals, popovers |
| `shadow-xl` | Large overlays |

Custom additions:

| Token | Value | Usage |
|---|---|---|
| `_____` | `_____` | _____ |

---

## Z-index

> *Document the layering scale. Avoid arbitrary z-index values.*

| Token | Value | Layer |
|---|---|---|
| `z-0` | 0 | Base content |
| `z-10` | 10 | Dropdowns, tooltips |
| `z-20` | 20 | Sticky headers |
| `z-30` | 30 | Drawers, sidebars |
| `z-40` | 40 | Modals |
| `z-50` | 50 | Toasts, notifications |

Custom additions:

| Token | Value | Usage |
|---|---|---|
| `_____` | `_____` | _____ |

---

## Maintaining this skill

When you (or anyone) modifies `tailwind.config.{js,ts}`:

1. Add/update the corresponding entry in this skill.
2. Document the *intended usage*, not just the value. The "why" is what makes tokens reusable.
3. If you deprecate a token, leave a note: `~~`old-name`~~ → use `new-name``.

This skill is the bridge between "what's technically available" (the config) and "what's appropriate to use" (the docs). Keep it tight.
