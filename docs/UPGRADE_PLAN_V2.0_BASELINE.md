# Lumina Numerology v2.0 → v2.1+ Upgrade Plan (Baseline Reference)

**Status:** Baseline approach document.

---

## Approach

You asked for a 4-step ritual: (1) take your base, (2) fill the gaps, (3) close the loopholes, (4) propose upgrades with *why/benefit* — then wait for approval before building. This plan is steps 2–4.

I pulled your live build (`hsharmagxi-debug.github.io/lumina-numerology/`, single 97 KB inline HTML+JS) and audited every tab, calculation, and DOM hook against the README's claims.

---

## 1. Gap analysis — README vs. shipped code

| Advertised | In source? | Notes |
|---|---|---|
| Pythagorean, Chaldean, Lo Shu | ✅ | `PYT`, `CHL`, Lo Shu grid all present |
| Vedic numerology | ⚠ Stub | Mentioned in tagline; no Vedic chart / Moolank / Bhagyank reduction logic distinct from Pythagorean. Currently leaning on Psychic + Destiny aliases. |
| Kabbalah | ⚠ Partial | One number `(total % 9) + 1` — that is NOT Kabbalah. Real Kabbalah numerology uses Hebrew gematria (22 letters → 1–400) reduced mod 9 with 22 meanings. Needs real Hebrew letter map + transliteration. |
| Life Path, Destiny, Soul, Expression, Maturity, Birth Day, Karmic Debt, Hidden Passion, Personal Year | ✅ | All present; Maturity = LP+Expr reduced (correct). |
| Essence Cycles, Transits, Bridge, Cornerstone/Capstone, Rational Thought, Planes of Expression, Diamond Spirit | ✅ | All wired. |
| 9 tabs | ⚠ Mismatch | README says 9 tabs by *number type*; the app actually has 9 *feature* tabs (Dashboard, Lo Shu, Cycles, Forecast, Compatibility, Solutions, Tools, Consensus, Profiles). README copy is misleading — needs rewrite, not new tabs. |
| Personal Month / Personal Day | ✅ | Computed but only surfaced in Forecast — not on Dashboard. |
| Offline-first | ✅ | Pure localStorage, no network calls. |
| 59+ functions | ✅ | Function count is real. |

---

## 2. Loopholes & bugs found in the v2 source

1. **Master-number preservation is inconsistent.** `reduce()` accepts a `m` flag, but several call sites (`rNM` for Personal Year/Month/Day, Cornerstone/Capstone numeric value, Kabbalah `% 9 + 1`) hard-reduce past 11/22/33. Result: an 11/22/33 PY silently becomes 2/4/6.
2. **Karmic Debt detection only checks the *final* reduction step.** The standard rule flags 13/14/16/19 appearing in *any* sub-total of LP, Expression, Soul, Personality, or Birthday. Current code only stores `lpRaw`; Expr/Soul/Pers sub-totals are not inspected.
3. **Chaldean chart skips 9.** That is correct (9 is sacred in Chaldean), but the code falls back to `0` for any letter mapping miss, which silently zero-pads Y→1 mistakes.
4. **`rationalThought = reduce(ntn(firstName) + d)`** uses *all letters* of first name. Classical formula uses **first-name consonants only**. Bug.
5. **Essence / Transit clock** uses calendar-year age (`age = currentYear - birthYear`), ignoring whether the birthday has passed. Off-by-one for ~half the year.
6. **`firstNameClean[0]`** for Cornerstone strips non-letters but does not handle hyphenated/double first names (e.g. "Mary-Anne") consistently with full-name parsing elsewhere.
7. **Diamond Spirit** uses first 9 letters of the *full sanitized name*. Source attribution is "Arabian system" but the actual Arabic abjad isn't used — it's Pythagorean values over the first 9 Latin letters. Current label is misleading.
8. **Date parsing trusts `<input type="date">`** with no timezone normalization; users in UTC− can see DOB roll back a day.
9. **localStorage has no schema version.** Any field rename in v2.1 will silently corrupt saved profiles.
10. **No input validation.** Empty/whitespace name → NaN cascades through 30+ orbs displaying "NaN".
11. **Five "world master" consensus voices** read all the same underlying numbers and paraphrase; they're presentation, not 5 systems. Current framing is misleading.
12. **XSS risk.** `innerHTML = ... ${p.nick} ...` interpolates user input directly into HTML in dozens of places.
13. **Starfield canvas** is `width="1920"` fixed; on retina mobile it blurs and on resize never re-renders.
14. **MIT claim** is in the README but there's no `LICENSE` file in the served bundle.

---

## 3. Missing features

- True **Vedic** module: Moolank, Bhagyank, Naam Ank, friendly/enemy number table, Rashi-linked planetary lord.
- True **Kabbalah** module: Hebrew gematria with proper transliteration table + 22 path meanings.
- **Pinnacles age boundaries** are computed but not annotated with "current pinnacle highlighted" on Dashboard.
- **Personal Day** scroll/calendar view (you have PD but no day-by-day grid).
- **Yearly forecast strip** (12 personal months at a glance) — data exists, view doesn't.
- **Name optimization suggestor**: given LP, suggest spellings whose Expression aligns.
- **Compatibility numeric matrix** between two profiles (LP×LP, Soul×Soul, Expr×Expr) — currently single-axis.
- **Export**: PDF / shareable image of the reading. Offline-friendly via `html2canvas` or native print stylesheet.
- **Accessibility**: zero ARIA, gold-on-navy contrast fails WCAG AA in places.
- **Mobile**: layout uses fixed pixel grids and a 1920-wide canvas; on a 390px viewport the starfield is fine but card grids overflow.

---

## 4. Proposed upgrades (grouped by tier)

### Tier A — Correctness & trust (strongly recommended)
- Single `reduce(n, {keepMasters})` + audit all call sites
- Karmic Debt sub-total scan across LP/Expr/Soul/Pers/Bday
- Fix `rationalThought` to first-name consonants
- DOB timezone-safe parsing
- Zod input validation + friendly errors
- HTML-escape every interpolated user string
- localStorage schema version + migration

### Tier B — Honesty about systems
- Real Kabbalah: Hebrew gematria table + transliteration + 22 path meanings
- Real Vedic: Moolank, Bhagyank, Naam Ank, planetary lord, friendly/enemy table
- Rename Diamond Spirit → "First-9 Diamond" OR implement Arabic abjad
- Reframe the 5 "Consensus Masters" as named interpretive lenses with *different system bias*

### Tier C — Decision-grade features
- **Name optimizer**: input target LP, output 5 spelling variants whose Expression matches
- **12-month forecast strip** + **daily PD calendar**
- **Compatibility matrix** (LP×LP, Soul×Soul, Expr×Expr, Bday×Bday) with red/yellow/green cells
- **PDF/PNG export** of the full reading
- **Compare two profiles side-by-side**

### Tier D — Platform & polish
- TanStack Start route per tab (`/dashboard`, `/lo-shu`, `/cycles`, …)
- Optional Lovable Cloud sync (email login) on top of localStorage
- Three design directions (you approved this)
- WCAG AA contrast pass on gold-on-navy
- Responsive starfield + DPR-aware canvas
- Add real MIT `LICENSE` file

---

**This is the reference baseline. Next file: comprehensive gap-coverage plan.**
