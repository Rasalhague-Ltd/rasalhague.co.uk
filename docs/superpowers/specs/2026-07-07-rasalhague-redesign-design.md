# Rasalhague Landing Site Redesign — "Modern Editorial"

**Date:** 2026-07-07
**Status:** Approved by user
**Scope:** `index.html` + `privacy.html` (UI/UX only)

## Goal

Redesign the two-page Rasalhague waitlist site to a refined light + gradient
aesthetic (reference: understory.io restraint, magma.build scroll-driven feel,
AOS-style reveals) with balanced, tasteful animation. UI/UX only.

## Hard constraints (non-negotiable)

1. **No data changes.** All numbers, statistics (650,000 / 94.7% / 84.2%),
   claims, the 9-item incident list, founder name, company details
   (Rasalhague Ltd, Companies House No. 17186277, 9 St Georges Place,
   Brighton, BN1 4GB), email address, and dates stay verbatim.
2. **Form logic untouched.** Same Mailchimp action URL, honeypot field,
   JSONP submit flow, success/error messaging behavior. Restyle only.
3. **Privacy page content unchanged** — it is a legal document. Restyle only.
4. **Copy may be lightly rephrased for flow, never for meaning.**
5. **Still 100% static:** two self-contained HTML files, inline CSS/JS,
   no build step, no external JS libraries. GitHub Pages compatible.
6. `CNAME` untouched.

## Design system

### Colors

| Token | Value | Use |
|---|---|---|
| `--bg` | `#FAFAF8` | page background |
| `--surface` | `#FFFFFF` | cards |
| `--ink` | `#131C28` | headlines, dark CTA panel |
| `--body` | `#3A4657` | paragraphs (≥4.5:1 contrast) |
| `--muted` | `#66707F` | labels, meta |
| `--line` | `#E7E9EE` | borders, rules |
| accent gradient | `#3B6FE0 → #7B5BE6` | buttons, gradient text, stat figures, icons |
| `--amber` | `#F5A83C` | the Rasalhague star ✦ only (signature accent) |

Soft radial blue→violet washes at low opacity in hero (top-right) and the
quote band.

### Typography

- **Bricolage Grotesque** — headlines (700/800), tight tracking.
- **Inter** — body, 17px, weight 400/500.
- **Space Grotesk** — uppercase letterspaced labels (12px, ~0.14em).
- Google Fonts with `display=swap`; system fallbacks.

### Shape & depth

- Cards: 20px radius, 1px `--line` border, layered soft shadows
  (`0 1px 2px` + `0 8px 24px` at ~6% ink), hover lift 4px + deeper shadow.
- Buttons: 12px radius, gradient fill, glow on hover/focus.
- Removed: wobbly hand-drawn border radii, dashed rules, squiggle
  underline SVG, sketchy doodles.

## Page: index.html

### Intro splash — REMOVED

The 3.6s blocking `#intro` overlay is deleted. The constellation moves into
the hero background:

- Faint star-field SVG absolutely positioned behind hero content.
- Ophiuchus constellation lines draw themselves over ~2s on load
  (stroke-dashoffset), non-blocking — headline visible at 0s.
- Alpha star (Rasalhague) twinkles in amber with a soft glow.
- Gentle parallax: stars translate at ~0.3× scroll speed.

### Hero

- Headline text unchanged; the key phrase ("No one is accountable when it
  fails.") gets gradient text instead of the hand-drawn underline.
- Headline → sub → form → stats stagger in on page load (~0.5s total).
- Waitlist form: white pill input + gradient button with arrow that slides
  on hover; focus glow. All Mailchimp mechanics preserved.
- Stats row: exact numbers + count-up preserved; figures in gradient text.

### Content sections

- All cards: white surface, fade-up 24px on scroll with stagger via
  hand-rolled IntersectionObserver (AOS behavior, zero dependencies).
- Left-column doodles → clean geometric line icons stroked in the accent
  gradient.
- Quote band ("damp" panel #1) → light gradient wash panel.
- Final CTA ("damp" panel #2) → **deep-ink night-sky panel**: `--ink`
  background, faint stars, amber Rasalhague star, gradient button. The one
  dark moment on the page.
- FAQ: same `<details>`/`<summary>` accordion (one-open-at-a-time behavior
  kept), smoother open animation, rotating +/× icon.
- Footer: cleaner layout, same data.

### Copy

Light rephrasing for flow permitted; meaning, tone, and every factual
element preserved. The 9-item "Lived Experience" list stays verbatim.

## Page: privacy.html

Same design system: header with brand + back link, same fonts/palette,
gentle fade-in on load, consistent footer. Content 100% unchanged.

## Accessibility, performance, fallbacks

- `prefers-reduced-motion: reduce` → all animation disabled, all content
  visible, stats render final values immediately.
- `<noscript>` → cards fully visible.
- Body contrast fixed: `#3A4657` @ 400 weight replaces `#55606E` @ 300.
- Semantic structure, aria labels, and heading order preserved.
- Optional nicety: `theme-color` meta.

## Verification plan

Open both pages in Chrome after implementation and check:

1. Hero renders correctly at 375 / 768 / 1440 px widths.
2. Scroll reveals fire once per section; stagger visible.
3. Form client-side validation error path (bad email → inline error).
4. FAQ accordion open/close, one-at-a-time.
5. `prefers-reduced-motion` emulation → no motion, everything visible.
6. Console free of errors; no 404s.
7. Privacy page styling consistent; back link works.

## Out of scope

- Any backend, analytics, new pages, or content strategy changes.
- Changing the Mailchimp list or form fields.
- SEO/meta rewrites beyond `theme-color`.
