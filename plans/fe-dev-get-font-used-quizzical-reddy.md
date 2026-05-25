# Plan: Add Philosopher font from portal-next into the launcher

## Context
The portal-next (`web/portal-next`) uses **Philosopher** (Google Font, weights 400 & 700) as its decorative serif font alongside Inter. The launcher (`client/launcher/ui/index.html`) already has Inter and Cinzel Decorative bundled locally but is missing Philosopher. The goal is to bring the same font into the launcher so both surfaces share a consistent typographic identity.

The launcher runs inside a Tauri webview with no guaranteed internet access, so the font must be bundled as local files — not loaded from the Google Fonts CDN.

## What's already there (do not duplicate)
- `client/launcher/ui/assets/fonts/Inter-Regular.woff2` — Inter 400 ✅
- `client/launcher/ui/assets/fonts/Inter-Medium.woff2` — Inter 500 ✅
- `client/launcher/ui/assets/fonts/CinzelDecorative-Bold.ttf` — display font ✅
- `--rc-font-display` and `--rc-font-body` CSS vars already defined in `:root` (lines 65–68)

## Files to modify
- **`client/launcher/ui/index.html`** — add `@font-face` blocks, CSS variable, and apply to elements
- **`client/launcher/ui/assets/fonts/`** — add two new font files

---

## Steps

### 1. Download Philosopher font files
Download woff2 files from the Google Fonts API (two weights used by portal-next):

```bash
curl -L "https://fonts.gstatic.com/s/philosopher/v23/vEFV2_5QCwIS4_Dhez5jcWjVamgc.woff2" \
  -o client/launcher/ui/assets/fonts/Philosopher-Regular.woff2

curl -L "https://fonts.gstatic.com/s/philosopher/v23/vEFX2_5QCwIS4_Dhez5jcVBhW4dG.woff2" \
  -o client/launcher/ui/assets/fonts/Philosopher-Bold.woff2
```

### 2. Add `@font-face` declarations
Insert after the existing Inter `@font-face` blocks (after line 36) in `index.html`:

```css
@font-face {
  font-family: "Philosopher";
  src: url("assets/fonts/Philosopher-Regular.woff2") format("woff2");
  font-weight: 400;
  font-style: normal;
  font-display: block;
}
@font-face {
  font-family: "Philosopher";
  src: url("assets/fonts/Philosopher-Bold.woff2") format("woff2");
  font-weight: 700;
  font-style: normal;
  font-display: block;
}
```

### 3. Add CSS variable to `:root`
Add alongside existing font tokens (after `--rc-font-mono` on line 68):

```css
--rc-font-philosopher: "Philosopher", Georgia, "Times New Roman", serif;
```

### 4. Apply Philosopher to launcher elements
Mirror the portal's usage — Philosopher as a serif accent for hero copy and section eyebrows:

- `.hero-tagline` → change `font-family` to `var(--rc-font-philosopher)`
- `.hero-body` → change `font-family` to `var(--rc-font-philosopher)`
- `.tab-eyebrow` → change `font-family` to `var(--rc-font-philosopher)`

Cinzel Decorative stays as `--rc-font-display` for the large title text (`.hero-display`). Philosopher slots in for the descriptive copy beneath it, matching the portal's pattern.

---

## Verification
1. Open `client/launcher/ui/index.html` in a browser via `python3 -m http.server 8080` from `client/launcher/ui/`
2. Check DevTools → Network: `Philosopher-Regular.woff2` and `Philosopher-Bold.woff2` load with 200
3. Inspect `.hero-tagline` — computed font-family should show `Philosopher`
4. Visually compare the hero section against `http://localhost:3000/r/` to confirm typographic consistency
