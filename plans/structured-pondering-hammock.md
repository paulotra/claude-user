# Plan: Rebuild Hero Section to Match Figma Design

## Context
The current Hero uses a centered single-column layout with a phone/desktop image and animated connector lines. The new Figma design (node 2213:6179) calls for a two-column layout — text on the left, a rounded hero image on the right — with three inline trust badge cards below the CTAs. The connector-line GSAP animations are no longer needed.

## Critical Files
- **Edit:** `src/components/Hero.tsx` — full layout rewrite
- **Keep as-is:** `src/components/EyebrowBadge.tsx`, `src/components/Button.tsx`, `src/app/globals.css`
- **Image used:** `public/hero/hero.png`
- **Icons used:** `public/icons/clock.svg`, `public/icons/shield.svg`, `public/icons/bolt.svg`

## What Changes in Hero.tsx

### Layout
- Remove: centered text-center column, `phone-wrapper`, `LinesLeft`, `LinesRightTop`, `LinesRightBottom` imports and JSX, connector-line GSAP timeline (`tl`)
- Add: two-column flex row — left column (text + CTAs + trust cards), right column (hero image)
- Background stays `#fffaf3` (or adjust to white per Figma — to verify)

### Left column content (top → bottom)
1. `<EyebrowBadge label="Try Now" text="Personalized Health Stack Report" />` (reuse existing component)
2. `<h1>` — "Your supplements and medications might be **working against** each other" — "working against" in `text-accent` (`#0f8a8d`)
3. `<p>` subtitle — "Hidden timing conflicts can reduce how well your stack works - sometimes **by up to 50%**." then paragraph about Absovex. "by up to 50%" in `text-pink` (`#f2188f`)
4. CTA row:
   - `<Button href="#" showArrow>See My Timing Conflicts</Button>`
   - `<Button href="#" variant="secondary">View Sample Report</Button>`
5. Trust badge row (3 cards, flex gap-5, each `w-[253px]`):
   - `/icons/clock.svg` + **"No account required."** / "Get your report instantly"
   - `/icons/shield.svg` + **"Your data stays private."** / "Never sold or shared"
   - `/icons/bolt.svg` + **"Educational tool."** / "Not medical advice"
   - Card styles: `bg-white rounded-card px-6 py-5 drop-shadow-[0px_14px_12px_rgba(0,34,31,0.04)] flex items-center gap-4`
   - Icon container: `bg-[#e8f7f7] border border-[#a7d2d2] rounded-[8px] p-[18px] size-[40px]` (teal tint, matches Figma)
   - Text: bold label + regular sub-label, both `text-accent` (`text-[#0f8a8d]`)

### Right column content
- `<Image src="/hero/hero.png" alt="..." width={568} height={558} />` with `rounded-2xl shadow-[0px_12px_32px_0px_rgba(154,176,177,0.32)]`

### GSAP — keep
- `gsap.to("body", { opacity: 1 })` fade-in
- `.scrub` stagger entrance animation on badge, h1, p, buttons, trust cards

### GSAP — remove
- `gsap.from(".phone", ...)` and `.phone-wrapper` scroll parallax
- Entire `tl` connector-line drawing timeline
- `ScrollTrigger` import (no longer needed)

## Implementation Notes
- Keep `"use client"` directive (GSAP requires it)
- Remove `LinesLeft`, `LinesRightTop`, `LinesRightBottom` imports entirely
- Keep `ShaderEffect` if it contributes to background texture (check visually)
- Text alignment: left-aligned on desktop, centered on mobile
- On mobile: stack image above text (or below), trust cards wrap

## Verification
1. Run `npm run dev` and open `localhost:3000`
2. Check desktop: two columns side by side, hero image on right, trust cards in a row
3. Check mobile: stacked layout, trust cards wrap or scroll
4. Confirm GSAP entrance animation still fires (badge → h1 → p → buttons → cards stagger in)
5. No console errors for missing imports (LinesLeft etc. removed cleanly)
