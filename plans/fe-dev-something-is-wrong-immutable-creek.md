# Fix: ScrollTrigger Race Condition with PinkAbsorb

## Context
When `<PinkAbsorb>` is present on the page, GSAP ScrollTrigger animations below it (WhatYouGet, Statistics word/line/scrub animations) sometimes fire with a delay or break on first load. Reloading fixes it because images are cached and available before ScrollTrigger calculates bounding boxes.

**Root cause:** PinkAbsorb contains two 1092×1092px spinning images (`/pink-pills-1.png`, `/pink-pills-2.png`). These load asynchronously after GSAP has already registered all ScrollTrigger instances and calculated their trigger positions. When the images finish loading they shift paint timing, leaving ScrollTrigger with stale measurements. There is no `ScrollTrigger.refresh()` call anywhere in the codebase.

## Critical Files
- `src/app/page.tsx` — where ScrollTrigger is registered and all animations are set up via `useGSAP`
- `src/components/SmoothScroll.tsx` — ScrollSmoother init via `useLayoutEffect`
- `src/components/PinkAbsorb.tsx` — the component with the large async images

## The Fix

Add a single `useEffect` in `src/app/page.tsx` that calls `ScrollTrigger.refresh()` after all page resources (including images) have finished loading:

```tsx
useEffect(() => {
  if (document.readyState === "complete") {
    ScrollTrigger.refresh();
  } else {
    window.addEventListener("load", ScrollTrigger.refresh);
    return () => window.removeEventListener("load", ScrollTrigger.refresh);
  }
}, []);
```

**Why `useEffect` not `useGSAP`:** `useGSAP` is for registering tweens/triggers. `ScrollTrigger.refresh()` is a one-shot imperative call after mount — `useEffect` with `[]` is the correct React lifecycle hook for this.

**Why `document.readyState` check:** In Next.js, hydration can happen after `window.load` has already fired (especially on client-side navigation). Without the check, the `load` listener would never fire if the page is already fully loaded.

**Why this fixes it:** `window.load` fires after all images, stylesheets, and subresources finish loading — including PinkAbsorb's spinning pills. At that point ScrollTrigger recalculates all trigger positions with accurate bounding boxes.

## Implementation Steps

1. In `src/app/page.tsx`, add `useEffect` to the existing React import line (it likely already imports `useGSAP` from `@gsap/react` — `useEffect` comes from `react`).
2. Add the `useEffect` block immediately after the existing `useGSAP` block.
3. No changes to any other file.

## What NOT to change
- Do not touch the `useGSAP` block — the animations themselves are correct.
- Do not touch `SmoothScroll.tsx` — ScrollSmoother init is fine.
- Do not move or restructure `PinkAbsorb` — its positioning is intentional.

## Verification
1. Hard-reload the page (Cmd+Shift+R) multiple times — scroll animations below PinkAbsorb should fire consistently every time.
2. Open DevTools → Network → throttle to Slow 3G, reload — animations should still trigger correctly even when images load slowly.
3. `tsc --noEmit` must pass clean.
