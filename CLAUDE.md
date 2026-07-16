# CLAUDE.md — working on callaeas.digital

Guidance for Claude (and humans) working in this repo. For the *design language and the
"why"*, read **[DESIGN.md](./DESIGN.md)**. This file is about *how to work here safely*.

## What this is

A one-page marketing site for **Callaeas Ltd**, a product & technology consultancy. Live at
**callaeas.digital**, served by **GitHub Pages from the `main` branch** (`CNAME` sets the
custom domain).

## Hard constraints

- **Everything lives in one self-contained `index.html`** — markup + an inline `<style>`
  block. No build step, no framework, no `package.json`, no bundler.
- **Fonts** load from **Google Fonts** via `<link>` (webfonts are fine here — this is a real
  site, not a sandboxed artifact). The site uses **one family: Recursive** (a variable font).
- **Deploy:** push to `main` → Pages rebuilds in ~1–2 min. If the change doesn't show, it's
  usually CDN/browser cache — hard-refresh.
- **One change per PR.** Branch → PR → merge to `main`. A merged PR is finished; start new
  work from fresh `main` (don't stack onto merged history).

## Invariants — do not break these

1. **The tagline stays on ONE line down to 320px.** It's `white-space: nowrap` with a
   vw-based `clamp()` (`font-size: clamp(1.2rem, 6vw, 2.65rem)`). If you add/lengthen it,
   re-check the 320px fit; shrink the clamp min before you ever let it wrap.
2. **`.wrap` sets horizontal padding ONLY**, via longhands (`padding-left/right`). Never give
   `.wrap` a `padding` shorthand — it will zero out the vertical padding on `.hero`,
   `section`, and `footer` (a class selector beats those element selectors). Set vertical
   padding on those with longhands too. This cascade collision has bitten the layout twice.
3. **Don't animate the tagline per-letter.** The tagline is a gradient clipped to text
   (`background-clip: text`). Animating `opacity`/`transform` on the individual `<span>`
   letters makes each glyph composite separately and **lose the clipped gradient → invisible
   text**. Animate the whole `.tagline` as one unit (see the `settle` keyframes).
4. **Keep the fallbacks.** The gradient text has a solid `color: var(--cool)` fallback under
   `@supports`; the font stack is `'Recursive', system-ui, …`; all motion is gated behind
   `@media (prefers-reduced-motion: no-preference)`. Both light and dark themes are
   first-class (tokens redefined under `@media (prefers-color-scheme: dark)`).

## Design system at a glance

One type system (**Recursive**), used across its axes:

- **Prose** → Recursive **Linear** with a touch of Casual (`CASL 0.32`).
- **Structural "chrome"** (section labels, the contact address, the footer) → Recursive
  **Mono** (`MONO 1`) — the "engineered/system" voice that echoes the tagline resolving onto
  the mono grid.

Colour is CSS custom properties on `:root` (`--ink/--ink-soft/--paper/--line/--accent` plus
the tagline arc `--warm/--warm-mid/--cool`), redefined for dark mode. Full palette, exact
axis coordinates, voice, and rationale live in **[DESIGN.md](./DESIGN.md)**. Change the
tokens, not hard-coded colours.

## Verifying a change (see it actually working)

**Human, locally:** open `index.html` in a browser (or `python3 -m http.server`), then:
resize down to ~320px (tagline must stay one line), toggle OS dark mode, and turn on "reduce
motion" to confirm the tagline is static-but-visible.

**Claude, in the remote env:** headless Chrome's `--screenshot` **ignores the mobile viewport
meta**, so it can't verify mobile — use **Playwright device emulation** instead:

- `npm i playwright-core` (browsers are pre-installed at `/opt/pw-browsers`; the Chrome
  binary is `/opt/pw-browsers/chromium-1194/chrome-linux/chrome`). Launch with that
  `executablePath`; emulate with `isMobile`/`viewport`/`colorScheme`/`reducedMotion`.
- To render the **real** webfonts offline (the proxy blocks `fonts.gstatic.com` from a plain
  fetch): `curl --cacert /root/.ccr/ca-bundle.crt -A "<a desktop-browser User-Agent>"` the
  Google Fonts CSS, download each `woff2`, rewrite the URLs to local files, and link that
  local CSS from a copy of `index.html`.
- Always check the matrix: **light + dark × desktop + 320/375px mobile**, with **animations
  on AND reduced-motion**. Watch for horizontal overflow (`documentElement.scrollWidth >
  innerWidth`) and confirm the tagline is one line and visible in every cell.

## Copy voice

Plain, direct, lightly witty; British spelling; short sentences. See DESIGN.md → Voice.
