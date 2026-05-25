# AGENTS.md

This file is loaded into every Codex session for this project. It captures the stack, conventions, and pointers to the deeper documentation so Codex has the context it needs from the first turn.

> **Fill in the placeholders** (marked `____`) when you first set this up. Keep it tight — anything that goes stale fast belongs in skills or memory, not here.

## Stack

- **Framework**: ____ (e.g. Vue 3 with Composition API, or React 18 with hooks)
- **Language**: TypeScript (strict mode)
- **Styling**: Tailwind CSS ____ (version)
- **Package manager**: ____ (npm / pnpm / yarn / bun)
- **Build tool**: ____ (Vite / webpack / Next.js / Nuxt)
- **State**: ____ (Pinia / Redux / Zustand / Jotai / local state only)
- **Testing**: ____ (Vitest + Testing Library / Jest / Playwright / Cypress)

## Where things live

- **UI components**: `src/components/`
- **Shared primitives**: `src/components/ui/` (or `src/ui/`)
- **Composables / hooks**: `src/composables/` (Vue) or `src/hooks/` (React)
- **Pages / routes**: ____
- **API client**: ____
- **Stores**: ____
- **Tailwind config**: `tailwind.config.____`
- **Global styles / CSS custom properties**: ____

## Scripts

Before committing, run:

```bash
____ typecheck && ____ lint && ____ test
```

Replace `____` with your package manager (`pnpm`, `npm run`, etc.). Common scripts:

- **Dev server**: `____ dev`
- **Build**: `____ build`
- **Typecheck**: `____ typecheck`
- **Lint**: `____ lint`
- **Format**: `____ format`
- **Test (unit)**: `____ test`
- **Test (e2e)**: `____ test:e2e`

## Conventions

### Components

- One component per file (default export), props/types as named exports.
- File name matches component name in PascalCase (e.g. `UserCard.tsx` / `UserCard.vue`).
- Components under ~150 lines (React) or ~200 lines (Vue SFC) — extract when growing past.

### Styling

- **Tailwind only** for components. No inline styles, CSS Modules, or styled-components in new code.
- **No arbitrary value classes** (`m-[8px]`, `text-[#1a1a1a]`). Use existing tokens or extend the config (with approval).
- Mobile-first responsive: base classes for mobile, `sm:`/`md:`/`lg:`/`xl:` for larger.

### TypeScript

- No `any`, no `@ts-ignore`. Use `unknown` and narrow.
- `interface` for object shapes, `type` for unions / computed types.
- Export prop types alongside components.

### Accessibility

- WCAG 2.1 AA minimum. Semantic HTML first, ARIA only when needed.
- Every interactive element keyboard-reachable with visible focus.
- Form inputs always labeled. Images always have `alt` (or `alt=""` for decorative).

## Agents and skills

This project uses Codex subagents and skills for consistent frontend work.

### Agents

- **`fe-dev`** — Builds and modifies UI. Use proactively for any frontend task.
- **`fe-qa`** — Reviews `fe-dev`'s work. Read-only. Use after changes to audit visual, functional, and accessibility quality.

Typical workflow:
```
Build the settings dropdown        → fe-dev implements
Use fe-qa to review the changes    → fe-qa audits and reports
[fix any blockers]                 → fe-dev applies fixes
```

### Skills

Both agents preload these project skills:

- **`tailwind-conventions`** — The strict Tailwind rules (no arbitrary values, ask-before-extend workflow).
- **`vue-react-standards`** — Framework idioms for Vue 3 and React + TypeScript hygiene.
- **`design-tokens`** — Catalog of this project's actual tokens (colors, spacing, fonts).
- **`a11y-bar`** — WCAG 2.1 AA checklist with concrete patterns.

When standards change, edit the **skill**, not the agents. Both agents will pick it up.

## What NOT to do in this project

- ____ (project-specific anti-patterns — fill in)
- ____
- ____

## Useful links

- Design system docs: ____
- Figma: ____
- Style guide: ____
