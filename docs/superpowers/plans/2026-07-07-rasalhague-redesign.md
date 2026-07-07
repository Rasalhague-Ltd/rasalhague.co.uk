# Rasalhague "Modern Editorial" Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.
>
> **REQUIRED DESIGN SUB-SKILLS (per user request):** Before writing any UI code in Task 1, load `frontend-design:frontend-design` and `emil-design-eng` (and `ecc:taste` if available) to calibrate visual judgment. After Task 3, run `impeccable:impeccable` as the dedicated polish pass (Task 4).

**Goal:** Redesign `index.html` + `privacy.html` to the approved "Modern Editorial" system — new palette/typography, hero constellation background (splash removed), tasteful scroll animation, dark night-sky CTA — with zero data/logic changes.

**Architecture:** Two self-contained static HTML files with inline CSS/JS. All existing JS hooks (`form.mc`, `.mc-msg`, `.field`, `.fig[data-count]`, `.card`, `.faq-item`) keep their class names so preserved scripts keep working. Splash `#intro` deleted; its constellation SVG is reused as the hero background.

**Tech Stack:** Vanilla HTML/CSS/JS, Google Fonts (Bricolage Grotesque, Inter, Space Grotesk), IntersectionObserver, GitHub Pages.

**Spec:** `docs/superpowers/specs/2026-07-07-rasalhague-redesign-design.md` — read it first; its Hard Constraints section governs every task.

## Global Constraints

- All numbers/stats/claims/names/addresses verbatim: 650,000 / 94.7% / 84.2%, the 9-item incident list, Hitesh Siwach, Rasalhague Ltd, Companies House No. 17186277, 9 St Georges Place Brighton BN1 4GB, hello@rasalhague.co.uk.
- Mailchimp form: action URL `https://rasalhague.us17.list-manage.com/subscribe/post?u=2fff9574227f866a9b6a51aea&id=29de4e7b8c&f_id=00c6eae3f0`, honeypot `b_2fff9574227f866a9b6a51aea_29de4e7b8c`, JSONP submit script — preserved byte-for-byte.
- `privacy.html` text content unchanged. `CNAME` untouched. No build step, no JS libraries.
- Copy may be rephrased for flow only, never meaning. Hero headline verbatim.
- `prefers-reduced-motion` disables all motion; `<noscript>` shows all content.

## Design tokens (single source of truth for both files)

```css
:root{
  --bg:#FAFAF8; --surface:#FFFFFF; --ink:#131C28; --body:#3A4657;
  --muted:#66707F; --line:#E7E9EE;
  --grad-a:#3B6FE0; --grad-b:#7B5BE6;
  --grad:linear-gradient(120deg,var(--grad-a),var(--grad-b));
  --amber:#F5A83C;
  --shadow-sm:0 1px 2px rgba(19,28,40,.05),0 8px 24px rgba(19,28,40,.06);
  --shadow-lg:0 2px 4px rgba(19,28,40,.06),0 16px 40px rgba(19,28,40,.10);
  --radius:20px;
}
```

Fonts (both files, `display=swap`):

```html
<link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,500..800&family=Inter:wght@400;500;600&family=Space+Grotesk:wght@400;500&display=swap" rel="stylesheet">
```

Roles: Bricolage Grotesque = h1/h2/stat figures/brand (700–800, tracking ≈ −0.02em); Inter = body 17px 400/500; Space Grotesk = uppercase labels 12px, letter-spacing .14em. Add `<meta name="theme-color" content="#FAFAF8">`.

---

### Task 1: Rewrite index.html (structure + CSS + hero)

**Files:**
- Modify: `index.html` (full rewrite; current file is the content source — copy all prose verbatim from it)

**Interfaces:**
- Produces: class names consumed by Task 2's JS — `.reveal` (scroll targets), `.fig[data-count]`, `form.mc`, `.faq-item`, `#skybg` (parallax target).

- [ ] **Step 1: Design-skill calibration.** Load `frontend-design:frontend-design` + `emil-design-eng` (+ `ecc:taste` if present). Keep their guidance in mind for spacing, hierarchy, and micro-detail throughout.

- [ ] **Step 2: Head + CSS.** Replace the whole `<style>` block. Keep `<title>`/`<meta description>` as-is; add theme-color + new fonts link. New CSS implements: tokens above; `body{background:var(--bg);color:var(--body);font:400 17px/1.65 Inter}`; `.wrap{max-width:1080px}`; headline styles; card/band/FAQ/footer/form styles; and this motion system:

```css
/* scroll reveal */
.reveal{opacity:0;transform:translateY(24px);transition:opacity .6s cubic-bezier(.22,1,.36,1),transform .6s cubic-bezier(.22,1,.36,1);}
.reveal.seen{opacity:1;transform:none;}
/* load stagger (hero only) */
[data-enter]{opacity:0;transform:translateY(16px);animation:enter .7s cubic-bezier(.22,1,.36,1) forwards;}
@keyframes enter{to{opacity:1;transform:none}}
/* gradient text */
.grad-text{background:var(--grad);-webkit-background-clip:text;background-clip:text;color:transparent;}
@media (prefers-reduced-motion:reduce){
  .reveal,[data-enter]{opacity:1 !important;transform:none !important;animation:none !important;transition:none !important;}
  *,*::before,*::after{animation:none !important;}
}
```

- [ ] **Step 3: Hero.** Delete the entire `#intro` overlay (current lines 146–182) and both `<script>` blocks' intro/splash/damp code (rebuilt in Task 2). Build hero:
  - `#skybg`: the existing constellation SVG (current lines 147–177) moved to an absolutely-positioned, `aria-hidden`, `pointer-events:none` layer behind hero content at ~35% opacity. Recolor: field stars `#99A3B2`; figure lines `#B3BDCB`; serpent path `stroke="url(#skygrad)"` (add `<linearGradient id="skygrad">` with `--grad-a`→`--grad-b`); alpha star `fill:#F5A83C` with `filter:drop-shadow(0 0 10px rgba(245,168,60,.8))` + existing twinkle keyframe; label text → Space Grotesk, `#66707F`. Lines draw on load via the existing `pathLength="1"` dash technique (~2s total, delays preserved from old intro CSS).
  - Soft wash: `.hero::before` radial gradient top-right, e.g. `radial-gradient(600px 420px at 85% 8%, rgba(123,91,230,.10), transparent 70%)` plus a blue one at 70%/20%.
  - Headline verbatim; wrap "No one is accountable when it fails." in `<span class="grad-text">`; delete the squiggle `<svg>`.
  - Stagger: `data-enter` + inline `animation-delay` 0/.08/.16/.24s on cap, h1, sub, form; stats row is a `.reveal`.
  - Form markup: input + button restyled (white field, 1px `--line`, focus ring `0 0 0 4px rgba(59,111,224,.15)`; button gradient bg, 12px radius, arrow `→` in a `<span>` that translates 3px on hover). Action/honeypot/names byte-identical.

- [ ] **Step 4: Content sections.** For each current card (lines 213–231): same prose, new shell `.card.reveal`. Left column: Space Grotesk label + 22×22 inline SVG line icon stroked `url(#icograd)` (define one hidden `<svg><defs><linearGradient id="icograd">` at body top). Icons: droplet (Problem), list (Lived Experience), anchor (Shift), layers (What It Is), numbered steps keeps `ol.steps`, shield-check (Evidence), key (Who It's For), user (Founder), question-mark (FAQ). Quote band → `.band-light.reveal` (white→lavender wash `linear-gradient(135deg,#FFFFFF, #F3F1FC)`, 1px line border). Final CTA → `.band-dark.reveal`: `background:var(--ink)` + two faint radial blue/violet glows, ~12 tiny star `<circle>`s + one amber star, white headline, duplicate form (same mechanics) with dark-adapted field. FAQ: same `<details>` structure; icon = two 2px spans forming +, rotates 45° when `[open]`. Footer: same data, flex layout, 1px top border.

- [ ] **Step 5: Copy check.** Diff old vs new prose; verify stats, the 9 incidents, founder/company details, and form attributes are verbatim. Rephrasing allowed only where flow demanded it.

- [ ] **Step 6: Commit** — `git add index.html && git commit -m "feat: redesign index.html to modern editorial system"`

### Task 2: index.html JS

**Files:**
- Modify: `index.html` (script blocks at end of body)

**Interfaces:**
- Consumes: `.reveal`, `#skybg`, `.fig[data-count]`, `form.mc`, `.faq-item` from Task 1.

- [ ] **Step 1: Preserve verbatim** from the old file: the FAQ one-open block, the Mailchimp JSONP submit block, and the count-up block (IntersectionObserver version, current line 242) — count-up now fires on scroll since no splash gates it. Update only the button label string if Task 1 changed it.

- [ ] **Step 2: Delete** the splash block (line 239), the intro/damp block (line 245), and every reference to `#intro`, `.damp`, `splash`.

- [ ] **Step 3: Add reveal + parallax:**

```js
(function(){
  var reduce=matchMedia('(prefers-reduced-motion: reduce)').matches;
  var els=document.querySelectorAll('.reveal');
  if(reduce||!('IntersectionObserver'in window)){els.forEach(function(e){e.classList.add('seen')});}
  else{var io=new IntersectionObserver(function(en){en.forEach(function(e){if(e.isIntersecting){e.target.classList.add('seen');io.unobserve(e.target);}});},{threshold:.12});
    els.forEach(function(e){io.observe(e)});}
  var sky=document.getElementById('skybg');
  if(sky&&!reduce){var t=false;addEventListener('scroll',function(){if(!t){t=true;requestAnimationFrame(function(){sky.style.transform='translateY('+(scrollY*0.15)+'px)';t=false;});}},{passive:true});}
})();
```

- [ ] **Step 4:** `<noscript><style>.reveal{opacity:1;transform:none}</style></noscript>`.

- [ ] **Step 5: Smoke-test** — open `index.html` in the browser; console must be clean; reveals, count-up, FAQ, and form validation error (type `x@x`, submit) all work.

- [ ] **Step 6: Commit** — `git commit -am "feat: scroll reveals, hero parallax; remove splash JS"`

### Task 3: privacy.html restyle

**Files:**
- Modify: `privacy.html` (`<head>` styles only; body text untouched)

- [ ] **Step 1:** Swap fonts link + `<style>` to the shared tokens. h1/h2 → Bricolage Grotesque (h1 clamp(2.2rem,5vw,3.2rem)); body → Inter 17px `--body`; `.updated`/`.footer-line`/`.back` → Space Grotesk caps `--muted`; links `--grad-a`, underline on hover; add a small header row (brand wordmark linking to `/`) consistent with index. Fade-in: `.container{animation:enter .6s cubic-bezier(.22,1,.36,1)}` + reduced-motion guard.

- [ ] **Step 2:** Verify with `git diff privacy.html` that no text content lines changed — style/head only.

- [ ] **Step 3: Commit** — `git commit -am "feat: restyle privacy page to match design system"`

### Task 4: Impeccable polish pass + full verification

- [ ] **Step 1:** Run `impeccable:impeccable` skill against both pages; apply its polish findings (spacing rhythm, type details, interaction feel).
- [ ] **Step 2:** Browser verification per spec: 375/768/1440px, reveals fire once, form error path, FAQ single-open, reduced-motion emulation, console/404 check, privacy back-link.
- [ ] **Step 3:** Fix findings, then final commit — `git commit -am "polish: impeccable pass on redesign"`.

## Self-review notes

- Spec coverage: splash removal→T1S3; hero constellation/parallax→T1S3+T2S3; gradient headline→T1S3; form preservation→T1S3+T2S1; icons/bands/FAQ/footer→T1S4; count-up→T2S1; privacy→T3; a11y/noscript→T1S2+T2S4; verification→T4. No gaps.
- No placeholders; all key code inline; content sourced verbatim from current file (safer than retyping).
- Type consistency: `.reveal`/`seen`, `#skybg`, `#icograd`, `#skygrad` used consistently across tasks.
