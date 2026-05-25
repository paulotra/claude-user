---
name: tailwind-conventions
description: Strict Tailwind CSS conventions for this project. Use whenever writing, reviewing, or modifying any Tailwind class — building UI components, fixing styling, adding new design values, or auditing existing classes. Covers the no-bracket-notation rule, the auto-extend-config workflow for new tokens, semantic naming conventions, class ordering, and responsive patterns. Apply this skill any time Tailwind utility classes are involved, even if the user doesn't explicitly mention Tailwind.
---

# Tailwind Conventions

This project uses Tailwind CSS with strict conventions. **The Tailwind config is the source of truth.** Every value used in a class must come from it.

## The hard rule

**Only use named Tailwind utilities whose values come from the config.** Examples of valid classes you compose with freely: `m-2`, `p-4`, `gap-6`, `text-sm`, `font-medium`, `text-content-primary`, `bg-surface-elevated`, `rounded-md`, `shadow-sm`, `w-full`, `max-w-prose`, `flex`, `grid-cols-3`, `items-center`, `hover:bg-surface-sunken`, `md:flex-row`, `focus:ring-2`.

These are the building blocks. Every styling choice composes from utilities like these.

**Forbidden: bracket notation.** Tailwind has a "JIT" syntax that lets you write any value inline using square brackets after a utility prefix. This project never uses that syntax. If you find yourself reaching for square brackets in a class, stop — the right move is to extend the config instead. See "The decision flow" below.

## The decision flow

Every time you need a styling value, walk this flow:

```
Need a value (color, spacing, size, radius, shadow, font, etc.)
        ↓
Does a Tailwind default utility already cover it?
        │
        ├── YES → use the default
        │
        └── NO  → Is there a project-specific utility in tailwind.config.{js,ts}?
                  │
                  ├── YES → use it
                  │
                  └── NO  → extend the config to add it, then use the new utility
```

**There is no fourth branch.** Bracket notation is not an option, ever. Not for prototyping, not "just this once," not for one-off values, not while waiting for approval.

## Tailwind defaults you should know by heart

Before reaching for bracket notation or assuming a value is missing, **recall the defaults**. Most common values already exist:

**Font sizes** — `text-xs` (12px), `text-sm` (14px), `text-base` (16px), `text-lg` (18px), `text-xl` (20px), `text-2xl` (24px), `text-3xl` (30px), `text-4xl` (36px), `text-5xl` (48px), `text-6xl` (60px), `text-7xl` (72px), `text-8xl` (96px), `text-9xl` (128px).

**Spacing** (4px scale) — `0.5` (2px), `1` (4px), `1.5` (6px), `2` (8px), `2.5` (10px), `3` (12px), `3.5` (14px), `4` (16px), `5` (20px), `6` (24px), `7` (28px), `8` (32px), `10` (40px), `12` (48px), `14` (56px), `16` (64px), `20` (80px), `24` (96px), `32` (128px), `40` (160px), `48` (192px), `56` (224px), `64` (256px), `72` (288px), `80` (320px), `96` (384px). Used as `m-*`, `p-*`, `gap-*`, `space-x-*`, `w-*`, `h-*`, etc.

**Line height** — `leading-none` (1), `leading-tight` (1.25), `leading-snug` (1.375), `leading-normal` (1.5), `leading-relaxed` (1.625), `leading-loose` (2). Also numeric: `leading-3` through `leading-10` (in rem units).

**Border radius** — `rounded-none` (0), `rounded-sm` (2px), `rounded` (4px), `rounded-md` (6px), `rounded-lg` (8px), `rounded-xl` (12px), `rounded-2xl` (16px), `rounded-3xl` (24px), `rounded-full`.

**Widths/heights** — `w-full`, `w-screen`, `w-fit`, `w-min`, `w-max`, `w-auto`, plus fractional (`w-1/2`, `w-1/3`, `w-2/3`, `w-1/4` ... `w-11/12`) and the spacing scale.

**If you don't remember whether a value exists as a default, default to the closest one rather than bracket notation.** If a design says 12px font size, the answer is `text-xs` (which is 12px) — not `text-[12px]`. If a design says 18px, the answer is `text-lg` (which is 18px), not `text-[18px]`. Bracket notation is never the right answer just because you're unsure of the default's name.

## Extending the config — the default path

When a needed value doesn't exist in the config, **add it.** This is normal, expected work — not a special escalation.

1. **Read `tailwind.config.{js,ts}`** to see what's already defined and pick a name that fits the existing structure.
2. **Edit the config** under `theme.extend.*` (never replace `theme.*` directly — that overrides Tailwind's defaults).
3. **Use a semantic name** when the value represents a role (`brand.primary`, `surface.elevated`, `danger`) rather than describing how it looks (`blue-deep`, `gray-light`).
4. **Mention the addition** in your output, briefly: "Added `colors.brand.deep` to `tailwind.config.ts` for the new hero accent." This keeps the user informed but doesn't block the work.
5. **Use the new utility** in your code.

The only time to **stop and ask first** is when the addition is structurally significant — adding a new top-level family of tokens, changing the spacing scale's rhythm, or introducing a value that contradicts what's already there (e.g. a second "primary" color). For one-off additions that fit the existing pattern, just do it and mention it.

### Examples of the workflow

**Need a color that doesn't exist:**

Read the config → find `colors.brand` already has `primary` and `secondary` → add `colors.brand.deep: '#2a4d7f'` under `theme.extend.colors.brand` → use `bg-brand-deep` in the component. Mention the addition in your summary.

**Need a spacing value that doesn't exist:**

Check the config → Tailwind's default 4px scale covers most things → if the design genuinely needs 52px (`13` on the scale), add `spacing: { '13': '3.25rem' }` under `theme.extend` → use `mt-13`. If a design calls for an off-grid value like 17px, push back — it's almost always meant to be 16px (`spacing.4`) or 20px (`spacing.5`).

**Need a font family that doesn't exist:**

Add it under `theme.extend.fontFamily` with a proper fallback stack ending in a generic family (`sans-serif`, `serif`, `monospace`).

## How to extend the config — patterns

Always use `theme.extend.*` so you add to Tailwind's defaults, not replace them.

### Colors

```js
// tailwind.config.ts
theme: {
  extend: {
    colors: {
      brand: {
        primary: '#2a4d7f',
        secondary: '#f5a623',
      },
      surface: {
        DEFAULT: '#ffffff',
        elevated: '#f9fafb',
        sunken: '#f3f4f6',
      },
      danger: {
        DEFAULT: '#dc2626',
        soft: '#fee2e2',
      },
    },
  },
}
```

Nest semantically. Use `DEFAULT` for the base value of a color family so `bg-danger` works without a suffix.

### Spacing

```js
theme: {
  extend: {
    spacing: {
      '4.5': '1.125rem',   // 18px
      '13': '3.25rem',     // 52px
      '128': '32rem',      // 512px
    },
  },
}
```

Stick to the 4px (0.25rem) rhythm of Tailwind's existing scale unless the design system genuinely uses something else.

### Fonts

```js
theme: {
  extend: {
    fontFamily: {
      sans: ['Inter', 'system-ui', 'sans-serif'],
      display: ['Cabinet Grotesk', 'sans-serif'],
      mono: ['JetBrains Mono', 'ui-monospace', 'monospace'],
    },
  },
}
```

Always include a fallback stack ending in a generic family.

### Other extensions

Same pattern for:
- `theme.extend.borderRadius` — radii
- `theme.extend.boxShadow` — shadows
- `theme.extend.zIndex` — layering scale
- `theme.extend.screens` — breakpoints (rarely needed; Tailwind defaults are usually fine)

## Class ordering

Follow the order `prettier-plugin-tailwindcss` enforces if it's installed. If it's not, use this manual order within a class string:

1. **Layout** — `flex`, `grid`, `block`, `hidden`, positioning (`absolute`, `relative`)
2. **Box model** — `w-`, `h-`, `m-`, `p-`, `gap-`
3. **Typography** — `text-`, `font-`, `leading-`, `tracking-`
4. **Visual** — `bg-`, `border-`, `rounded-`, `shadow-`
5. **State variants** — `hover:`, `focus:`, `active:`, `disabled:`
6. **Responsive variants** — `sm:`, `md:`, `lg:`, `xl:`, `2xl:`

Within state and responsive groups, repeat the same layout → box → typography → visual order.

## Responsive — mobile-first, always

Tailwind is mobile-first by default. Base classes apply to small screens; prefixed variants add at larger breakpoints.

```html
<!-- Mobile-first: works with the framework -->
<div class="flex flex-col gap-2 md:flex-row md:gap-4">
```

Standard breakpoints:
- Base (no prefix) — mobile, ~375px
- `sm:` — 640px+
- `md:` — 768px+
- `lg:` — 1024px+
- `xl:` — 1280px+

Never write separate `@media` queries when a variant exists. Avoid `max-*:` variants except in rare cases — they invert the mobile-first model.

## What NOT to do

- **No bracket notation.** Ever. If the value doesn't exist as a named utility, extend the config instead.
- **No mixed styling systems.** If the project is Tailwind, don't introduce inline `style={{...}}`, styled-components, CSS Modules, or vanilla CSS files for components.
- **No `!important`** via `!` prefix unless overriding a third-party library style. There's almost always a better way.
- **No redundant classes.** `p-2 p-4` (the latter wins, but it's noise). `text-base text-sm` (same issue).
- **No `@apply` in component CSS** to recreate utility combinations. If a pattern repeats, extract a component, not a class.

## When in doubt

If a value seems unusual or arbitrary in the design, push back briefly: "The design specifies 17px here, but the project's spacing scale is 4px-based — should this be 16px (`p-4`) or 20px (`p-5`)?" Designers often round to whatever a tool spits out; the underlying intent is almost always grid-aligned.