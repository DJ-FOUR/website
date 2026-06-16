# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file React portfolio website for a Chinese UI/UX designer (杜俊涛). No build step — all dependencies loaded from CDN, JSX transpiled in-browser via Babel Standalone.

## How to run

Open `index.html` directly in a browser, or serve with any static file server:
```bash
npx serve .
# or
python -m http.server 8080
```

There is no `package.json`, no npm scripts, no build step.

## Architecture

**Single file: `index.html`** — the entire application (425 lines).

### Dependency stack (all CDN, no bundler)
- **React 18** + **ReactDOM 18** (loaded from unpkg, development builds)
- **React Router 6.3** (`MemoryRouter` — browser URL never changes, navigation is purely in-memory)
- **history 5** (required by React Router 6.3 UMD build)
- **Tailwind CSS v3** (CDN with inline `tailwind.config` extending colors, fonts, spacing, font sizes)
- **Babel Standalone 6** (transpiles JSX in-browser from `<script type="text/babel">`)
- **Google Fonts**: Plus Jakarta Sans (headlines), Noto Sans SC (body), Geist (mono labels)
- **Material Symbols Outlined** (icon font, used for chevron navigation arrows)

### Route table (MemoryRouter)
| Path | Component | Content |
|---|---|---|
| `/` | `HomePage` | Portfolio overview with hero + 3 category cards (mobile/web/game) |
| `/mobile` | `MobilePage` | 校园咖啡GO — campus coffee ordering app |
| `/web` | `WebPage` | 自然守望者 — wildlife conservation responsive website |
| `/game` | `GamePage` | 喵噗小镇 — anime-style gacha game UI |

### Component structure (all in one file)
- `useIntersectionObserver` — custom hook for scroll-triggered fade-in animations (queries `.fade-in-up` elements)
- `useScrollToTop` — scrolls window to top on route change
- `NavBar` — sticky top nav with brand link, 4 route links (hidden on mobile: `hidden md:flex`), and a non-functional "联系我" button (no onClick)
- `HomePage` — 3 linked cards with background images (`image/01.jpg`, `image/02.jpg`, `image/03.jpg`), each with hover effects (scale, translate, glowing border)
- `MobilePage` — project description with glass-card panel + horizontally scrollable image carousel with smooth-scroll chevron buttons and **mouse drag-to-scroll**. Shows a single mockup (`image/B.jpg`) and a nav diagram (`image/daohang.jpg`).
- `WebPage` — project description + horizontally scrollable image carousel showing 5 site screenshots (`image/site_1.jpg` through `image/site_5.jpg`)
- `GamePage` — project description header + single full-width image (`image/game.jpg`). Does NOT use `useIntersectionObserver`.
- `App` — layout: NavBar + `<Routes>`
- `RouterWrapper` — wraps App in `MemoryRouter`, renders into `#root`

### Styling approach
- **Tailwind utility classes** for most layout/spacing/colors
- **Custom CSS** in a `<style>` block for: glassmorphism panel (`.glass-card`), scroll-triggered fade-in (`.fade-in-up`), custom scrollbar (`.custom-scrollbar`)
- **Inline styles** used for dynamic background images, gradient overlays, and masking (`WebkitMaskImage`)
- **Dark mode only** — enforced via `<html class="dark">` and `darkMode: "class"` in Tailwind config
- Design tokens follow Material Design 3 naming (surface, on-surface, primary-fixed-dim, etc.)

### Key design patterns
- Glassmorphism UI: `backdrop-filter: blur(20px)` with semi-transparent backgrounds and subtle borders
- Scroll-triggered animations via Intersection Observer on `.fade-in-up` elements
- Horizontal drag-to-scroll on MobilePage (custom mouse event handlers on the scroll container)
- CSSTransition-based hover effects on card components (translate, scale, opacity transitions)
- All images served from local `/image/` directory as optimized JPEG
- `<img>` tags use `loading="lazy"` + `decoding="async"` for off-screen images; CSS background images on homepage cards load eagerly (no native lazy-load for CSS backgrounds)

## Notes

- App references optimized JPEG files: `01.jpg`–`03.jpg` (home cards), `B.jpg` + `daohang.jpg` (mobile page), `site_1.jpg`–`site_5.jpg` (web page), `game.jpg` (game page). Total ~4.7 MB across all images.
- React Router uses `MemoryRouter`, so there are no bookmarkable URLs or browser history support. Changing to `BrowserRouter` or `HashRouter` would require a server-side rewrite rule for SPA routing.
- The app uses React 18's `createRoot` API (not the legacy `ReactDOM.render`).
- Tailwind CDN with plugins (`forms, container-queries`) — the `container-queries` plugin name appears misspelled (should be `container-queries` → the CDN may silently ignore it). Not functionally impactful since the app doesn't use container queries.
- The "联系我" (Contact Me) button in NavBar is purely decorative (no `onClick` handler).
- `MobilePage`'s drag-to-scroll uses mutable module-level variables (`isDown`, `startX`, `scrollLeft`) rather than React state/refs — this works because React re-renders don't interfere with the raw DOM scroll position, but it's not idiomatic React.
