# DESIGN.md — Callaeas design language

The design system behind callaeas.digital, and the reasoning behind it. For *how to work in
the repo* (constraints, invariants, verification), see **[CLAUDE.md](./CLAUDE.md)**. The
source of truth is always `index.html`; keep this doc in step with it.

---

## Concept

**"digital spaghetti, untangled."** Callaeas comes into messy digital businesses and makes
sense of them. The site says that with *type itself*, not just words: a tangled, cursive,
warm phrase visibly straightens into calm, upright, structured letterforms.

**Brand-first hierarchy.** "**Callaeas**" is the brand — a large, confident wordmark. "digital
spaghetti, untangled" is its **tagline/strapline**, set beneath it. The brand is the anchor;
the tagline is the single expressive moment.

---

## Typography — one system: Recursive

The whole page is set in **[Recursive](https://www.recursive.design/)**, a variable font, used
across its axes (`MONO`, `CASL` casual, `wght`, `slnt`, `CRSV` cursive). Using **one** family
is the point: the brand and the body share the same DNA, and the tagline *is* that font
travelling from its expressive extreme to its resolved one. The relationship is literal, not a
pairing of two faces hoping to look related.

**The rule:**

- **Prose → Recursive Linear, lightly Casual.** Warm but readable.
- **Structural "chrome" → Recursive Mono.** Section labels, the contact address, the footer.
  Monospace is the "engineered / shipped-code" voice — and it rhymes with the tagline
  resolving onto the mono grid at "untangled". Product-and-technology, in the type.

### Exact coordinates (must match `index.html`)

| Role | `font-variation-settings` | Size / notes |
|---|---|---|
| **Brand** `.brand` | `MONO 0, CASL 0, wght 860, slnt 0, CRSV 0` | `clamp(3.2rem, 11vw, 6.6rem)`, `line-height .98`, `letter-spacing -.02em` |
| **Tagline** `.tagline` | per-letter ramp (below) | one line, `clamp(1.2rem, 6vw, 2.65rem)`, `white-space:nowrap`, gradient text |
| **Body prose** `body` | `MONO 0, CASL 0.32, wght 400, slnt 0, CRSV 0` | `line-height 1.62` |
| **Labels** `h2` | `MONO 1, CASL 0, wght 560, slnt 0, CRSV 0` | `0.76rem`, uppercase, `letter-spacing .14em`, `--accent` |
| **Address** `.contact-line` | `MONO 1, CASL 0, wght 480, slnt 0, CRSV 0` | `1.1rem` |
| **Footer** `footer` | `MONO 1, CASL 0, wght 420, slnt 0, CRSV 0` | `0.8rem`, `--ink-soft` |

**The tagline ramp.** "digital spaghetti, untangled" is split into per-letter `<span>`s whose
axes interpolate left→right so every letter is calmer than the last:

- `CASL` **1 → 0** (casual/brush → linear)
- `slnt` **−14 → 0** (leaning → upright)
- `wght` **760 → 400** (heavy knot → light and calm)
- `CRSV` **1** through "digital spaghetti," then **0** at "untangled" (cursive forms → roman)
- `MONO` **0** through the tangle, then ramps **0 → 1** across "untangled" (snaps onto the mono grid)

The `.m` class on the "untangled" letters pulls `letter-spacing` slightly (`-.012em`) to
absorb the mono widening. Values are precomputed literals in the markup — regenerate the whole
run if you change the ramp, don't hand-edit individual letters.

---

## Colour

Tokens are CSS custom properties on `:root`, redefined under
`@media (prefers-color-scheme: dark)`. **Always style through the tokens.**

| Token | Light | Dark | Role |
|---|---|---|---|
| `--ink` | `#1d2530` | `#e8e6df` | primary text, brand |
| `--ink-soft` | `#4d5a6b` | `#a3aab6` | body / muted text |
| `--paper` | `#f6f5f1` | `#171c24` | background (warm paper) |
| `--line` | `#dcdad2` | `#2c3440` | hairline rules |
| `--accent` | `#2547d0` | `#7d95f5` | links, labels — the "house indigo" |
| `--warm` | `#b5670c` | `#f6a63c` | tagline gradient: warm end (amber) |
| `--warm-mid` | `#9a4a5f` | `#cf7a9e` | tagline gradient: rose bridge |
| `--cool` | `#2547d0` | `#7d95f5` | tagline gradient: cool end (= `--accent`) |

**The tagline arc.** A 90° gradient clipped to the tagline text runs **amber → rose → the
house indigo** (stops: warm 0–40%, warm-mid 66%, cool 100%). The idea: *chaos literally cools
into the colour the site already uses for links* — so the arc feels native, not decorative.

**Guardrails.**

- **Legibility on paper:** the light-mode amber is *deep* (`#b5670c`, ~4:1 on `--paper`), not a
  bright gold that would wash out. Dark mode can go brighter (`#f6a63c`) on the dark ground.
- **Avoid the clichés** flagged during design: warm-cream + terracotta, and the decorative
  purple→blue gradient. The warm end is amber (no orange-brown terracotta), and the arc is
  motivated by the metaphor, resolving into the *existing* accent.
- The ground stays the established **warm paper** — this is a refresh, not a re-palette.

---

## Motion

Deliberately minimal. On load the tagline **settles into place as one unit** — a short
opacity+rise (`settle`, 0.7s), gated behind `prefers-reduced-motion: no-preference`. Under
reduced motion it renders immediately, static and fully visible. (It must animate as a whole;
per-letter animation breaks the gradient text-clip — see CLAUDE.md.)

---

## Voice & copy

Plain, direct, a little dry-witty. British spelling. Short sentences. Name the mess, then the
resolution. Examples already on the page:

> We come into digital businesses with a lot going on — product, tech, and the commercial
> knots in between — and make sense of the mess.

> We come in, make sense of it, and get things launched.

Avoid jargon and hype. The tagline carries the personality; the body is calm and confident.

---

## Principles / anti-patterns

- **Spend boldness in one place.** The tagline is the single loud moment; everything else
  (brand aside) stays quiet — muted body, hairline rules, one accent.
- **One type family, one accent, one idea.** Resist adding a second display face or a second
  accent colour.
- **Readability is non-negotiable** — the expressive extreme still has to be read.
- **Both themes are first-class.** Design and check light *and* dark, never invert blindly.
- **Tokens, not literals.** New colours become `:root` variables with a dark-mode value.

---

## Decision log

Newest first. Short "why"s so future changes have context.

- **2026-07 · Document the system.** Added `CLAUDE.md` + this file so the design language and
  working conventions live in the repo, not just in the CSS.
- **2026-07 · Unify the whole page on Recursive; prose Linear+Casual, chrome Mono.** The body
  had been left in a neutral Geist while the hero was bespoke — the page split into "designed
  top, default bottom." One family fixes that and the Mono chrome reinforces the tagline idea.
- **2026-07 · Warm end = deep amber (`#b5670c`), not crimson-rose.** Owner preference for
  amber; chosen deep enough to stay legible on the paper ground and to dodge the
  terracotta-on-cream cliché.
- **2026-07 · Comma in the tagline** ("spaghetti, untangled") — a small breath, riding the
  same slant/weight as the end of the tangled word.
- **2026-07 · One variable font (Recursive), tagline morphs per-letter.** The morph *is* the
  brand idea; beats pairing two unrelated faces. Retired the earlier Dancing-Script + Geist
  attempt and the decorative wavy-line SVG (the type now performs the untangle).
- **2026-07 · `.wrap` is horizontal-padding-only (longhands).** A `padding` shorthand on
  `.wrap` was silently zeroing the vertical padding on `.hero`/`section`/`footer` (class beats
  element selector) — the fix that let section spacing finally open up.
- **2026-07 · Animate the tagline as one unit.** Per-letter opacity animation broke the
  `background-clip:text` gradient and made the glyphs invisible.
- **2026-07 · Tagline must hold one line to 320px** via `nowrap` + a vw-based `clamp()`.
