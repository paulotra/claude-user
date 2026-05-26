---
name: vue-react-standards
description: Framework standards for Vue 3 (Composition API) and React (functional components + hooks), with TypeScript. Use whenever writing, modifying, or reviewing component code — building components, refactoring, adding hooks/composables, handling state, or typing props. Covers component shape, hook/composable rules, state patterns, TypeScript hygiene, and idiom matching. Apply this skill any time component code is involved, even if the user doesn't explicitly mention the framework.
---

# Vue & React Standards

This project may use either Vue 3 or React. **Always detect the stack first** by reading `package.json`. Then apply the matching framework's standards from below. The TypeScript section applies to both.

## Stack detection

Read `package.json` and look at dependencies:

- **Vue project**: has `vue` in dependencies, usually `^3.x`. May also have `@vue/composition-api`, `pinia`, `vue-router`, `nuxt`, `vite-plugin-vue`.
- **React project**: has `react` and `react-dom`. May also have `next`, `remix`, `react-router-dom`, `redux`, `zustand`, `jotai`.

Check `tsconfig.json` for strict mode (`"strict": true`) — assume strict is on unless proven otherwise.

## React standards

### Component shape

- **Functional components only.** No class components.
- **Default export the component**, named exports for types and helpers.
- **Keep components under ~150 lines.** If a component grows past that, extract subcomponents or move logic into custom hooks.
- **One component per file** in most cases. Tightly coupled subcomponents can share a file.

```tsx
// ✅ Good
interface UserCardProps {
  user: User;
  onSelect?: (id: string) => void;
}

export default function UserCard({ user, onSelect }: UserCardProps) {
  // ...
}

export type { UserCardProps };
```

### Hooks

- **Follow the rules of hooks.** No conditional hooks, no hooks in loops, no hooks in nested functions.
- **Correct dependency arrays in `useEffect`, `useMemo`, `useCallback`.** Never suppress `react-hooks/exhaustive-deps` — fix the actual dependency issue.
- **Custom hooks** start with `use` and live in `src/hooks/` (or wherever the project keeps them).
- **No state mutations.** Treat state as immutable. Use the setter, never mutate the object directly.

```tsx
// ❌ Bad
setUser(user => {
  user.name = 'New';
  return user;
});

// ✅ Good
setUser(user => ({ ...user, name: 'New' }));
```

### State

The progression is:

1. **`useState`** for simple local state.
2. **`useReducer`** when state has multiple interdependent fields (e.g. a form with 6+ fields, a wizard with steps).
3. **Lift state up** when multiple components need it.
4. **Context** for state that's truly app-wide but doesn't change often (theme, auth, locale).
5. **A store** (Redux/Zustand/Jotai) **only if the project already uses one.** Don't introduce a new state library.

### Memoization

- **Don't memoize prematurely.** `useMemo` and `useCallback` have a cost (closure creation + dependency check) that often exceeds what they save.
- **Memoize when**: a value is expensive to compute (>1ms consistently), or a callback is passed to a memoized child where reference equality matters.
- **Don't memoize**: simple object/array literals, primitives, computations that take microseconds.

### Lists

- **Always provide a stable `key`.** Use the item's ID, not the array index (unless the list never reorders).

```tsx
// ❌ Bad
{items.map((item, i) => <Item key={i} {...item} />)}

// ✅ Good
{items.map(item => <Item key={item.id} {...item} />)}
```

### SVG icons (React / Next.js only)

> **Apply this section only when the detected stack is React or Next.js.** Skip entirely for Vue projects.

This project uses a custom SVG icon set imported as React components via **SVGR** (`vite-plugin-svgr`).

**File location** — SVGs live in `src/icons/`, named in kebab-case matching their purpose (e.g. `arrow-right.svg`, `close.svg`).

**SVG file requirements** — every SVG in `src/icons/` must have:
- `fill="currentColor"` — color controlled from outside via Tailwind.
- `width="1em" height="1em"` — so the icon scales with `font-size` (`text-*` classes).

**Icon component** — never import individual SVGs at call sites. Use a single shared `Icon` component that bulk-imports all SVGs via `import.meta.glob` and resolves by name:

```tsx
// src/components/ui/Icon.tsx
import type { FC, SVGProps } from 'react';

const modules = import.meta.glob<{ default: FC<SVGProps<SVGSVGElement>> }>(
  '/src/icons/*.svg',
  { eager: true, query: '?react' }
);

interface IconProps extends SVGProps<SVGSVGElement> {
  name: string;
  className?: string;
}

export default function Icon({ name, className, ...props }: IconProps) {
  const SvgIcon = modules[`/src/icons/${name}.svg`]?.default;
  if (!SvgIcon) return null;
  return <SvgIcon className={className} {...props} />;
}

export type { IconProps };
```

**Usage** — pass the kebab-case filename (without `.svg`) as `name`:

```tsx
<Icon name="arrow-right" className="text-sm" />
<Icon name="close" className="text-black text-xl" />
```

**Rules**:
- **Size via `text-*` font-size utilities** (`text-xs`, `text-sm`, `text-base`, `text-lg`, `text-xl`, etc.) — never `w-*`/`h-*` or `width`/`height` props.
- **Default color is `text-black`** unless the Figma design specifies otherwise.
- **Never import SVGs individually** at call sites — always go through `<Icon name="..." />`.
- Never use `<img src="...svg" />` for icons.
- Never write inline `<svg>` markup in component files.

```tsx
// ❌ Bad
import ArrowRightIcon from '@/icons/arrow-right.svg?react';
<ArrowRightIcon className="w-5 h-5" />

// ✅ Good
<Icon name="arrow-right" className="text-black text-sm" />
```

## Images (both frameworks)

This project uses a **Claude + Figma MCP workflow** — a Figma link will always be provided. Use the Figma MCP to read the design and export assets directly from it.

- **Always import images as modules** — never reference them as raw string paths.
- **Always export images from Figma at 2x scale** — use the Figma MCP to export at 2x, never 1x.
- **File naming** — suffix exported files with `@2x` (e.g. `banner@2x.png`, `logo@2x.webp`).

```tsx
// ❌ Bad
<img src="/assets/hero.png" />

// ✅ Good
import heroImage from '@/assets/hero@2x.png';
<img src={heroImage} alt="Hero" />
```

In Vue:
```vue
<script setup lang="ts">
import heroImage from '@/assets/hero@2x.png';
</script>

<template>
  <img :src="heroImage" alt="Hero" />
</template>
```

## Vue 3 standards

### Component shape

- **Composition API with `<script setup lang="ts">`** unless the project clearly uses Options API throughout — then match it.
- **Single File Components (SFC)** with `<template>`, `<script setup>`, `<style>` in that order.
- **Keep SFCs under ~200 lines.** If growing past that, extract composables for logic or subcomponents for template parts.
- **No manual `setup()` function** when `<script setup>` is available — it's strictly better.

```vue
<script setup lang="ts">
interface Props {
  user: User;
  selectable?: boolean;
}

const props = defineProps<Props>();
const emit = defineEmits<{
  select: [id: string];
}>();
</script>

<template>
  <article>
    <!-- ... -->
  </article>
</template>
```

### Props and emits

- **Type with `defineProps<...>()` and `defineEmits<...>()`** generic syntax, not the object syntax.
- **Default values** via `withDefaults`:
  ```ts
  const props = withDefaults(defineProps<Props>(), {
    selectable: false,
  });
  ```
- **Emit names use kebab-case** in templates, but the TS type uses camelCase keys (Vue handles the conversion).

### Reactivity

- **`ref` for primitives and replacement-style updates.** Access value with `.value` in script, auto-unwrapped in templates.
- **`reactive` for objects you mutate in place.** Don't destructure or reassign — you'll lose reactivity.
- **`computed` for derived state.** Always prefer `computed` over a watcher that sets a ref.
- **`watch` for side effects.** Use `watchEffect` only when the dependencies are obvious from the body.
- **Don't mix `ref` and `reactive` for the same conceptual shape.** Pick one approach per piece of state.

```ts
// ✅ Good — primitives as refs
const count = ref(0);
const isLoading = ref(false);

// ✅ Good — complex objects as reactive
const formState = reactive({
  name: '',
  email: '',
  errors: {} as Record<string, string>,
});

// ❌ Bad — destructuring loses reactivity
const { name, email } = formState; // these are NOT reactive
```

### Composables

- **Custom composables** start with `use` and live in `src/composables/` (or wherever the project keeps them).
- **Return refs/reactives, not raw values.** Consumers expect reactivity.
- **One responsibility per composable.** `useUser()`, `useFormValidation()`, not `useEverything()`.

### State management

- **Pinia is the standard** for Vue 3 — only use it if the project already has stores set up.
- **Don't introduce Vuex** in new code; it's legacy for Vue 3 projects.

## TypeScript (both frameworks)

### Hygiene rules

- **No `any`.** If a type is genuinely unknown, use `unknown` and narrow it with type guards.
- **No `@ts-ignore`.** Use `@ts-expect-error` only with an inline comment explaining why, and prefer fixing the actual type.
- **No non-null assertions (`!`)** unless the non-null guarantee is obvious from the surrounding code. Use proper narrowing or runtime checks.
- **Strict mode assumed.** All strict flags on: `strictNullChecks`, `noImplicitAny`, etc.

### Types vs interfaces

- **`interface`** for object shapes that may be extended, especially component props.
- **`type`** for unions, intersections, mapped types, conditional types, and utility-type compositions.

```ts
// Object shape that consumers might extend → interface
interface UserCardProps {
  user: User;
  onSelect?: (id: string) => void;
}

// Union or computed → type
type Status = 'idle' | 'loading' | 'success' | 'error';
type Nullable<T> = T | null;
```

### Exports

- **Export prop/emit types alongside components** so consumers can reuse them:
  ```ts
  export type { UserCardProps };
  // or in Vue:
  defineExpose({ ... });
  ```

### Generics

- **Use generics for reusable hooks/composables** that work with multiple types:
  ```ts
  function useFetch<T>(url: string): { data: Ref<T | null>; ... }
  ```
- **Name generic parameters meaningfully** when more than one (`TData`, `TError`) rather than `T, U, V`.

## When in doubt: match the project

If the codebase has an established pattern that differs from the rules above, **match the codebase**. Consistency within a project beats following a generic standard. Flag the divergence as a follow-up if you think it's worth a broader conversation, but don't unilaterally change patterns in a single PR.
