# Design System Rules — for Figma MCP integration

## Context: this is the free v2.0 baseline; a paid v2.1+ SaaS successor exists

Per `README.md` and `docs/UPGRADE_PLAN_V2.0_BASELINE.md`, this **public** repo is "Lumina
Numerology v2.0" — free, offline, no ads/tracking/payment, MIT-licensed. The private repo
`hsharmagxi-debug/Lumina-SaaS` is the in-progress **v2.1+ SaaS evolution** of this exact same
product (adds Firebase auth, Gemini AI, a server) — confirmed by near-identical CSS class
architecture (`.card`, `.ct`, `.logo`, `.badge`, `.mcell`, `.nval`) and `<title>` tags one version
apart ("Lumina v2.0" here vs. "Lumina v2.1" there). **Treat these two repos as one product's two
stages, not unrelated apps** — a design change to one should usually be considered for the other,
and the token/typography differences between them (below) look like an intentional v2.0→v2.1
palette revision, not drift.

Like `Lumina-SaaS`, **the entire application lives in one monolithic `index.html`** (1,169 lines,
inline `<style>` block) — there is no `src/`, no build step, no component framework at all here;
this repo is even more minimal than `Lumina-SaaS` (no Vite/React scaffold present whatsoever, just
plain static HTML/CSS/JS, consistent with the "offline-first, zero dependencies" pitch in the
README).

## 1. Token Definitions

- Single `:root` block inline in `index.html`'s `<style>` (`index.html:9`):
  ```css
  --bg:#03060F        --bg2:#070D1E       --bg3:#0A1228
  --s1:rgba(8,16,40,.97)   --s2:rgba(12,24,58,.88)   --s3:rgba(20,35,75,.6)
  --card:rgba(7,13,30,.97)
  --gold:#C9A84C      --gl:#F0D080        --gd:rgba(201,168,76,.1)   --gb:rgba(201,168,76,.4)
  --teal:#2EC4B6      --purple:#B86EF0    --red:#E05C5C   --green:#4CAF80   --blue:#4A9EBF
  --txt:#F5EDD5       --txh:#F5EDD5       --txd:#8A9BC0   --txm:#5A6A90
  --bdr:rgba(201,168,76,.16)   --bdb:rgba(201,168,76,.42)
  ```
  Same token *naming* scheme as `Lumina-SaaS` (`s1`/`s2`/`s3` surface steps, `gd`/`gb` gold-dim/
  gold-border, `txt`/`txh`/`txd`/`txm` text hierarchy) — **but different actual values**:
  - **This repo (v2.0)**: deep navy-black background (`--bg:#03060F`), cooler/brighter gold
    (`--gold:#C9A84C`), warm cream text (`--txt:#F5EDD5`).
  - **`Lumina-SaaS` (v2.1)**: near-pure-black background (`--bg:#050505`), a slightly darker/
    warmer gold (`--gold:#C5A059`), neutral grey text (`--txt:#e5e5e5`).
  When asked to update "Lumina's" colors, confirm which version/repo is meant — the two have
  genuinely diverged, this isn't the same value copy-pasted.
- **Typography**: 4 font families here (vs. 6 in `Lumina-SaaS`) — `Cinzel Decorative` (400/700/900,
  logo), `Cinzel` (400–700, section labels/numerals — `.ct` uses `Cinzel` here, where `Lumina-SaaS`
  uses `Marcellus` for the equivalent `.ct` class), `Raleway` (300–700, body), `JetBrains Mono`
  (400/500, badges/nav). **`Lumina-SaaS` added `Cormorant Garamond` and `Marcellus`** on top of
  this set — v2.1 is a strict typographic superset of v2.0, not a replacement.
- Same shared-token-name convention as `Lumina-SaaS` for glow/rim treatment (`--gd`/`--gb`),
  surface elevation (`--s1`/`--s2`/`--s3`), and text hierarchy (`--txt`/`--txh`/`--txd`/`--txm`) —
  reuse those exact 4-letter abbreviations for consistency if extending either repo's tokens.

## 2. Component Library

- None — plain HTML/CSS classes in one file, identical architecture to `Lumina-SaaS` (see that
  repo's `CLAUDE.md` §2 for the general pattern: `.card`, `.badge` + color variants, `.mcell`,
  `.nval`/`.mnum` glowing-numeral display). Reuse those same class conventions here; they're
  shared between the two repos almost verbatim.
- No Storybook, no component docs.

## 3. Frameworks & Libraries

- **None** — no `package.json` was found relevant to a JS framework at repo root; this is a
  static, dependency-free HTML/CSS/JS page consistent with the README's "offline-first, no
  build step" pitch. Do not introduce a framework/bundler here without confirming that's an
  intended departure from the "zero fluff, works offline" positioning.
- `docs/` holds planning documents, not code: `PLAN_COMPARISON_ANALYSIS.md`,
  `PROGRESS_REPORT_PHASE1.md`, `UPGRADE_PLAN_COMPREHENSIVE_GAPS.md`,
  `UPGRADE_PLAN_V2.0_BASELINE.md` — read `UPGRADE_PLAN_V2.0_BASELINE.md` for the actual rationale
  behind the v2.0→v2.1 direction if working on either repo's design.

## 4. Asset Management

- No separate asset directory found — likely everything (if any images/icons exist) is inline in
  `index.html` (emoji/Unicode glyphs or inline SVG), matching `Lumina-SaaS`'s pattern of no
  dedicated icon library actually wired into the static-HTML UI.

## 5. Icon System

- No icon library dependency (no `package.json` at all) — any icon-like glyphs in `index.html`
  are almost certainly emoji or hand-placed Unicode/inline-SVG, not a component-based icon system.
  Check the specific element's markup directly before assuming an icon font or SVG sprite exists.

## 6. Styling Approach

- Identical methodology to `Lumina-SaaS`: one inline `<style>` block, hand-written CSS with
  custom-property tokens, no Tailwind, no CSS-in-JS, no CSS Modules. Reusable local patterns
  (`.card` with the gradient-line `::before` highlight, badge color-variant recipe, glowing
  numeral treatment via `text-shadow`, three-gradient ambient background, animated `#magic-cursor`/
  stardust trail if present — verify against this file's own markup since the two repos may have
  diverged slightly in interaction polish) — see `Lumina-SaaS`'s `CLAUDE.md` §6 for the full
  documented pattern list, which applies here too.
- `main{padding:18px 16px;max-width:800px;margin:0 auto}` — same mobile-first single-column
  container constraint as `Lumina-SaaS`.

## 7. Project Structure

```
index.html   The entire application — markup + inline CSS design tokens, no build step
docs/        Planning/upgrade documents (v2.0→v2.1 rationale, gap analysis, progress reports)
README.md    Product description — free/offline/MIT-licensed positioning
```

**Recommendation for Figma integration**: decide explicitly whether new Figma-sourced design work
targets this free v2.0 baseline, the private v2.1+ `Lumina-SaaS` evolution, or both in parallel —
given how closely their class architecture already tracks, a token change made in one should
usually be evaluated for the other rather than let them silently diverge further.
