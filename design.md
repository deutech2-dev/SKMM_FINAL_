# Sarita Kunj Mitra Mandal — Design Documentation

A single-page scroll site for a community/devotional mandal (Kasarwadi, Pune), built with a cinematic, editorial-agency aesthetic (dark stacking cards, oversized display type, scroll-driven motion).

---

## 1. Tech Stack

| Layer | Tool |
|---|---|
| Styling | Tailwind CSS (CDN, JIT) + custom CSS in `<style>` |
| Smooth scroll | Lenis |
| Animation / scroll triggers | GSAP + ScrollTrigger |
| Icons | Iconify (`iconify-icon` web component, Solar & FA7 icon sets) |
| Fonts | Google Fonts (`Syncopate`), Pretendard (CDN), Poppins / Noto Sans Devanagari (declared, not linked) |

No build step — everything runs client-side from CDNs.

---

## 2. Color Palette

Defined as CSS custom properties on `:root`:

| Token | Value | Usage |
|---|---|---|
| `--c-bg` | `#E3E1DC` (warm off-white/stone) | Page background, light sections |
| `--c-dark` | `#121212` | Base text color, footer background family |
| `--c-accent` | `#4A5D48` (deep sage green) | Accent headings, icons, highlighted text |
| Card sections | `#1a1a1a` | Dark stacked-card background |
| Footer | `#111` | Full-bleed sticky footer |
| Text on dark | `#fff`, `text-gray-400`, `text-gray-500` | Secondary/muted text on dark backgrounds |
| Text on light | `text-gray-600`, `text-gray-700` | Body copy on light sections |

**Palette character:** muted, earthy, low-saturation — off-white stone + near-black + a single sage-green accent. No bright/saturated colors.

---

## 3. Typography

| Role | Font | Notes |
|---|---|---|
| Display / headings | `Syncopate` (`--font-display`) | Used via `.display` class; geometric, wide-tracking, uppercase-leaning headline font |
| Body | `Pretendard` (`--font-body`), overridden in practice by `Poppins`, `Noto Sans Devanagari` | `body` rule sets `font-family: 'Poppins', 'Noto Sans Devanagari', sans-serif` after the variable — the Devanagari fallback suggests bilingual (English/Marathi) content support |

**Type treatment patterns:**
- Hero headline: `text-[7vw]` fluid sizing, `leading-none`, `tracking-tighter`, uppercase
- Section eyebrows: `text-xs uppercase tracking-widest` (labels like "WORK", "SADAIV BHAKTI")
- Card numbers (01–04): large, low-opacity (`opacity-20`) background-colored numerals as decorative layer
- Body copy: `font-light`, relaxed leading, smaller sizes on dark cards (`text-sm md:text-base`)
- Custom utility classes `.marathi-text`, `.english-text` exist for bilingual line-height/vertical-align control (line-height 1.4)

---

## 4. Layout Structure

1. **Loader** — full-screen black preloader with "SKMM" wordmark + progress bar, fades/slides out on load.
2. **Fixed nav** — top bar, `mix-blend-difference` for automatic contrast over any background, wordmark left / nav links right.
3. **Hero section** — full-viewport (`h-screen`), background image at 50% brightness, two-line oversized title ("SARITA KUNJ" / "MITRA MANDAL"), tagline below with letter-spaced uppercase text.
4. **Intro section** — two-column grid (heading left, body + location/growth metadata right) on the light background.
5. **Card stack section** (dark, `--c-dark` background):
   - "WORK" eyebrow + large section title ("FESTIVALS AND EVENTS")
   - 4 sticky, scroll-stacked cards (`position: sticky`), each a 2-column grid: text content (number, title, description, "MANAGEMENT" CTA) + full-bleed image
   - Cards: Ganesh Utsav, Members, Female Participation, Felicitation
   - Responsive: collapses to single column under 768px, image moves above text
6. **Two centered info sections** ("Management and Planning", "Festivals and Events") — icon + heading + paragraph + supporting image, on light background.
7. **Sticky full-screen footer** — fixed positioned, revealed as the page content scrolls past it (parallax-style reveal via `margin-bottom: 100vh` on `.wrapper`), large tagline with prayer icon, back-to-home link, copyright line, faint background image at 10% opacity + grayscale.
8. **Noise overlay** — fixed, full-screen, very low opacity (0.04) SVG fractal-noise texture layered above everything (`z-index: 9000`) for a subtle film-grain effect.

---

## 5. Motion / Interaction Design

All animation is driven by **GSAP + ScrollTrigger**, with **Lenis** providing inertial smooth scrolling that GSAP's RAF loop is synced to.

| Effect | Mechanism |
|---|---|
| Preloader | Timeline: bar fills → text fades up/out → loader panel slides up (`yPercent: -100`) to reveal site; body opacity flips 0→1 on complete |
| Hero title reveal | Each line wrapped in `overflow-hidden` span, inner span starts `translateY(110%)`, animates to `y: 0` with stagger on load |
| Hero tagline | Fades in (`opacity: 0 → 1`) after a delay |
| Hero image parallax | `yPercent: 20` scrub tied to scroll position (background moves slower than foreground) |
| Word-by-word text reveal | `.split-animate` elements: JS splits text into per-word spans (`.word-wrap` / `.word-inner`), each word slides up from `translateY(110%)` with a small stagger, triggered when scrolled into view (`top 90%`), reverses on scroll-out |
| Card stack | Each card is `position: sticky`; as the *next* card enters, the *current* card's inner shrinks (`scale: 0.92`) and dims (`opacity: 0.3`) via scrubbed ScrollTrigger — creates a layered "cards passing underneath" effect |
| Footer reveal | Footer content animates up + fades/scales in as the page scrolls to the bottom (scrubbed) |

**Motion character:** slow, cinematic, editorial — heavy use of `power4`/`power3` easing, scroll-scrubbed (not just on-load) animation, and layered z-index tricks (sticky footer under scrolling wrapper with matching bottom margin) rather than simple fades.

---

## 6. Component Patterns

- **Buttons/links:** text-only, uppercase, `tracking-widest`, underlined via `border-b`, with an icon that translates on hover (`group-hover:translate-x-1`) — no filled buttons anywhere.
- **Cards:** dark panels, thin `1px` translucent white border, large drop shadow, asymmetric grid (`1fr 1.2fr`) favoring the image side.
- **Icons:** Iconify web components (`solar:*`, `fa7-solid:pray`), used sparingly as accents above headings.
- **Dividers:** thin 1px lines (`bg-black/10`, `bg-black/20`) used as minimal section separators instead of borders/boxes.

---

## 7. Content Notes (for whoever fills this in)

- Placeholder/lorem ipsum text still present in all 4 stack cards and both closing sections — needs real copy.
- Image paths reference a local `./img/` folder (`5.jpeg`, `14.jpeg`, `19.jpeg`, `10.jpeg`, `16.jpeg`, `22.jpeg`, `23.jpeg`) — not uploaded with this file, will 404 until supplied.
- Nav currently has a single placeholder link ("LOREM").
- Copyright: "© 2012 SARITA KUNJ MITRA MANDAL, KASARWADI, PUNE".
- Content is bilingual-aware (English display copy + Marathi phrase "KAYAM APLYA SEVET" in the intro headline), consistent with the Devanagari font fallback.

---

## 8. Responsive Behavior

Single breakpoint at `768px` (Tailwind `md:`):
- Card grid: 2-column → 1-column, image reorders above text (`order: -1`)
- Card height/offsets adjust (`top: 10vh → 5vh`, `height: 80vh → 85vh`)
- Content padding reduces (`4rem → 2rem`)
- Nav links hidden below `md:` (no visible mobile menu toggle implemented)
