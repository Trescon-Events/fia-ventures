# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Single-page marketing site for **FIA Ventures**, a Dubai (DIFC)-headquartered VC firm. The entire site is one self-contained `index.html` — no build tooling, no package manager, no tests. Assets sit beside it:

- `hero-bg.mp4` — 4 MB looping hero background video
- `logo.svg` — standalone brand mark (the inline header/footer SVGs are duplicates, not references)
- `logos/` — portfolio-company logos referenced from the Portfolio section
- `photos/` — team headshots (Team section) and founder portraits (Testimonials)

## Running it

There is no dev server config. To preview:

```bash
open index.html              # quick check, but the <video> may not autoplay over file://
python3 -m http.server 8000  # then visit http://localhost:8000
```

Use the local server when changing anything video-, font-, or fetch-related; `file://` quirks will mislead you otherwise.

## Architecture

`index.html` is structured as: inline Tailwind CDN + inline `tailwind.config` (custom `gold` and `dark` color palettes) → inline `<style>` block (custom utilities, animations, hover states) → `<body>` with six top-level `<section>` blocks → inline `<script>` at the bottom.

**Section IDs are load-bearing.** The nav links, the active-nav IntersectionObserver, and the scroll buttons all key off these exact IDs — keep them in sync if you rename or add sections: `home`, `about`, `focus`, `portfolio`, `team`, `contact`. The observer's `sectionIds` array in the bottom script must match.

**Inline JS modules** (vanilla, no framework, all in the trailing `<script>`):
1. Navbar scroll-state toggle (`.scrolled` class on `#navbar`)
2. Mobile menu open/close + auto-close on link click
3. Active-nav IntersectionObserver tied to `sectionIds`
4. `.reveal` on-scroll fade-in observer (one-shot, unobserves after firing)
5. Focus-card stat animation — reads `data-target` (count-up %) and `data-width` (bar fill) off `.stat-label` / `.stat-bar`
6. Testimonial carousel — auto-advance every 6s, prev/next buttons, generated dots, touch swipe (>40px delta)

**Styling conventions worth knowing:**
- Custom Tailwind colors: `gold-300/400/500/600`, `dark-400…900`. Don't reach for stock Tailwind grays — palette is intentional.
- `.gold-text` (gradient), `.gold-glow` / `.gold-glow-sm` (box-shadow), `.card-hover` (lift + glow on hover), `.gold-divider` (horizontal gradient rule), `.section-num` (faint background numeral) recur throughout. Reuse these rather than inventing parallel styles.
- Fonts: `font-serif` = Crimson Pro (headings, quotes); `font-sans` = Space Grotesk (body, default).

**Portfolio and team cards** follow a fixed pattern — logo/photo container, tag chips (`.portfolio-tag`), serif heading, description, footer row. When adding a new entry, copy an existing card verbatim and edit; the layout depends on the exact class stack (e.g. `flex flex-col` + `flex-1` on the paragraph so footers align across the grid).

**Logo `<img>` tags use an `onerror` fallback** that swaps in a lettermark div. Preserve this pattern when adding portfolio companies — it's the only graceful-degrade path since there's no JS error handling elsewhere.
