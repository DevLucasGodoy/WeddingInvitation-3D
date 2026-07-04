# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file digital wedding invitation (`index.html`) simulating opening a physical invite via CSS 3D flip animations. Vanilla HTML/CSS/JS — no build step, no dependencies, no framework, no tests. Portuguese (pt-BR).

## Running

Open `index.html` directly in a browser, or serve the folder statically (GitHub Pages / Vercel / any static host). The file is fully self-contained.

Note: the invitation images are loaded from **GitHub raw URLs** (`raw.githubusercontent.com/DevLucasGodoy/WeddingInvitation-3D/.../public/N.png`), not from the local `public/` folder. Editing local `public/*.png` will NOT change what renders until the change is pushed to the `main` branch on GitHub. To preview local images, point the `<img src>` at the relative `public/N.png` paths instead.

## Architecture

Everything lives in `index.html`: `<style>` in `<head>`, markup in `<body>`, `<script>` before `</body>`.

The interaction is a **3-state machine driven by a single CSS class on `<body>`** (`state-1` / `state-2` / `state-3`). The click handler mutates `currentState` and reassigns `body.className`; all visual change is CSS reacting to that class:

- `state-1` → envelope, `.card-3d` at `rotateY(0deg)`
- `state-2` → invite front, `rotateY(180deg)`
- `state-3` → invite back, `rotateY(360deg)`, reveals `.website-button`

State 3 clicking loops back to state 2 (front↔back), so state 1 (envelope) is only ever seen once. Because the envelope and back-face are coplanar at `0°`, the loop would flash the envelope when returning from the verso; to prevent this, JS adds a persistent `opened` class after the first `1→2` flip completes (`card` `transitionend`), and `.opened .envelope-face { visibility: hidden }` retires the envelope for the rest of the session. `render()` therefore toggles only the `state-*` class (via `classList`), never a full `className =` assignment, so `opened`/`is-flipping` survive.

The 3D flip relies on `.container` perspective + nested `preserve-3d` layers with three stacked `.card-face` layers using `backface-visibility: hidden`. Face-to-image mapping (in the markup): `envelope-face` = `3.png`, `front-face` = `1.png`, `back-face` = `2.png`. Envelope and back-face both sit at `rotateY(0)`; the envelope (`z-index: 3`) covers back-face in state 1, and `.state-3 .back-face { z-index: 4 }` flips the priority so `2.png` wins when the card returns to 0° — this is why the envelope is NOT hidden with `display:none` (which would pop instead of flipping away).

Layer stack (outer→inner): `.container` (perspective) → `.scene` (holds lift animation + ground `.card-shadow`) → `.card-tilt` (pointer parallax via `--tilt-x/--tilt-y`) → `.card-3d` (the flip) → faces. Pointer parallax + glare and the `is-flipping` lift animation are JS-driven; both are gated on `(hover: hover) and (pointer: fine)` and disabled under `prefers-reduced-motion`.

The website button is excluded from the body click handler (`e.target.closest(".website-button")` guard) so its own click opens the couple's site instead of advancing state.

## Editing notes

- Common customizations (images, final button URL, background color, instruction text) are documented in README.md under "Personalização".
- There is no text instruction/hint element. The only "tap to open" affordance is the animated `.seal-glow` ring on the envelope; the only bottom overlay is `.website-button` (state 3). `render()` just swaps `body.className`.
- The envelope (state 1) has a `.seal-glow` overlay div inside `.envelope-face` — a halo + pulsing ring positioned (`top: 44%`) over the wax seal in `3.png` to signal "tap to open". State 1 also runs idle animations (`.state-1 .scene` float, `.state-1 .card-shadow` breathe); all of these are killed under `prefers-reduced-motion`. Brand palette: gold `#c9a24a`, olive `#2e3a12`, cream `#f5f2e9`.
- Responsiveness is **fluid, not breakpoint-driven**: fonts/paddings/offsets use `clamp()`, and `.container` width is `min(92vw, 600px, (100dvh − --reserved-bottom) / 1.414)` so the card is bounded by available **height** as well as width and never scrolls in any orientation. `.scene` uses `aspect-ratio: 1 / 1.414` (not the old padding-bottom hack). the card is centered with **symmetric** body padding, and `--reserved-bottom` (on `body`) is subtracted inside the width `calc()` only — it shrinks the card enough that its bottom margin clears the fixed instruction/button, without pushing the card off-center (an earlier `padding-bottom: var(--reserved-bottom)` caused it to sit too high). Every `dvh` value is preceded by a `vh` fallback line for browsers without `dvh`. The only remaining media query is a low-landscape tweak (`max-height: 520px and orientation: landscape`) plus the `prefers-reduced-motion` block.
