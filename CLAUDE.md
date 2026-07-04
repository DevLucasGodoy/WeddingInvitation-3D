# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file digital wedding invitation (`index.html`) simulating opening a physical invite via CSS 3D flip animations. Vanilla HTML/CSS/JS — no build step, no dependencies, no framework, no tests. Portuguese (pt-BR).

## Running

Open `index.html` directly in a browser, or serve the folder statically (GitHub Pages / Vercel / any static host). The file is fully self-contained.

Images are **local** (`public/N.{avif,webp,png}`), served relative from the deploy — no external hosts. Each face is a `<picture>` that picks AVIF, else WebP, else the PNG fallback. Because the browser prefers AVIF/WebP, editing only `public/N.png` will NOT change what renders — regenerate all three formats (see README "Personalização" for the `sharp-cli` commands). Deploy config lives in `vercel.json` (cache + security headers); with `cleanUrls: true` the site serves at `/`.

## Architecture

Everything lives in `index.html`: `<style>` in `<head>`, markup in `<body>`, `<script>` before `</body>`.

The interaction is a **3-state machine driven by a single CSS class on `<body>`** (`state-1` / `state-2` / `state-3`). The click handler mutates `currentState` and reassigns `body.className`; all visual change is CSS reacting to that class:

- `state-1` → envelope, `.card-3d` at `rotateY(0deg)`
- `state-2` → invite front, `rotateY(180deg)`
- `state-3` → invite back, `rotateY(360deg)`, reveals `.website-button`

State 3 clicking loops back to state 2 (front↔back), so state 1 (envelope) is only ever seen once. Because the envelope and back-face are coplanar at `0°`, the loop would flash the envelope when returning from the verso; to prevent this, JS adds a persistent `opened` class after the first `1→2` flip completes (`card` `transitionend`), and `.opened .envelope-face { visibility: hidden }` retires the envelope for the rest of the session. `render()` therefore toggles only the `state-*` class (via `classList`), never a full `className =` assignment, so `opened`/`is-flipping` survive.

The 3D flip relies on `.container` perspective + nested `preserve-3d` layers with three stacked `.card-face` layers using `backface-visibility: hidden`. Face-to-image mapping (in the markup, each face a `<picture>` with AVIF/WebP/PNG sources; `N` = the image number): `envelope-face` = `3.*`, `front-face` = `1.*`, `back-face` = `2.*`. `.card-face picture { display: contents }` keeps the wrapper layout-transparent so the `<img>` fills the face; `<img>` carries `width/height` (1240×1748, kills CLS) and `fetchpriority` (envelope `high`, verso `low`). Envelope and back-face both sit at `rotateY(0)`; the envelope (`z-index: 3`) covers back-face in state 1, and `.state-3 .back-face { z-index: 4 }` flips the priority so `2.png` wins when the card returns to 0° — this is why the envelope is NOT hidden with `display:none` (which would pop instead of flipping away).

Layer stack (outer→inner): `.container` (perspective) → `.scene` (holds lift animation + ground `.card-shadow`) → `.card-tilt` (pointer parallax via `--tilt-x/--tilt-y`) → `.card-3d` (the flip) → faces. Pointer parallax + glare and the `is-flipping` lift animation are JS-driven; both are gated on `(hover: hover) and (pointer: fine)` and disabled under `prefers-reduced-motion`.

The website button is excluded from the body click handler (`e.target.closest(".website-button")` guard) so its own click opens the couple's site instead of advancing state.

## Editing notes

- Common customizations (images, final button URL, background color, instruction text) are documented in README.md under "Personalização".
- There is no text instruction/hint element. The only "tap to open" affordance is the animated `.seal-glow` ring on the envelope; the only bottom overlay is `.website-button` (state 3). `render()` just swaps `body.className`.
- The envelope (state 1) has a `.seal-glow` overlay div inside `.envelope-face` — a halo + pulsing ring positioned (`top: 44%`) over the wax seal in `3.png` to signal "tap to open". State 1 also runs idle animations (`.state-1 .scene` float, `.state-1 .card-shadow` breathe); all of these are killed under `prefers-reduced-motion`. Brand palette: gold `#c9a24a`, olive `#2e3a12`, cream `#f5f2e9`.
- Responsiveness is **fluid, not breakpoint-driven**: fonts/paddings/offsets use `clamp()`, and `.container` width is `min(92vw, 600px, (100dvh − --reserved-bottom) / 1.414)` so the card is bounded by available **height** as well as width and never scrolls in any orientation. `.scene` uses `aspect-ratio: 1 / 1.414` (not the old padding-bottom hack). the card is centered with **symmetric** body padding, and `--reserved-bottom` (on `body`) is subtracted inside the width `calc()` only — it shrinks the card enough that its bottom margin clears the fixed instruction/button, without pushing the card off-center (an earlier `padding-bottom: var(--reserved-bottom)` caused it to sit too high). Every `dvh` value is preceded by a `vh` fallback line for browsers without `dvh`. The only remaining media query is a low-landscape tweak (`max-height: 520px and orientation: landscape`) plus the `prefers-reduced-motion` block.
