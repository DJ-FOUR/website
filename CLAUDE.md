# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file React portfolio website for a Chinese UI/UX designer (杜俊涛). No build step — all dependencies loaded from CDN, JSX transpiled in-browser via Babel Standalone.

## How to run

Open `AAA.html` directly in a browser, or serve with any static file server:
```bash
npx serve .
# or
python -m http.server 8080
```

There is no `package.json`, no npm scripts, no build step.

## Architecture

**Single file: `AAA.html`** — the entire application (~474 lines).

### Dependency stack (all CDN, no bundler)
- **React 18** + **ReactDOM 18** (loaded from unpkg)
- **React Router 6.3** (`MemoryRouter` — browser URL never changes, navigation is purely in-memory)
- **Tailwind CSS v3** (CDN with inline `tailwind.config` extending colors, fonts, spacing, font sizes)
- **Babel Standalone 6** (transpiles JSX in-browser from `<script type="text/babel">`)
- **Google Fonts**: Plus Jakarta Sans (headlines), Noto Sans SC (body), Geist (mono labels)
- **Material Symbols Outlined** (icon font)

### Route table (MemoryRouter, lines ~455-460)
| Path | Component | Content |
|---|---|---|
| `/` | `HomePage` | Portfolio overview with hero + 3 category cards |
| `/mobile` | `MobilePage` | Campus Go coffee ordering app |
| `/web` | `WebPage` | NexWealth asset management dashboard |
| `/game` | `GamePage` | Aetheria Saga RPG HUD design |

### Component structure (all in one file)
- `useIntersectionObserver` — custom hook for scroll-triggered fade-in animations
- `useScrollToTop` — scrolls window to top on route change
- `NavBar` — sticky top nav with brand link and 4 route links (hidden on mobile: `hidden md:flex`)
- `Footer` — placeholder links (all `href="#"`, non-functional)
- `HomePage`, `MobilePage`, `WebPage`, `GamePage` — page components
- `App` — layout: NavBar + `<Routes>` + Footer
- `RouterWrapper` — wraps App in `MemoryRouter`, renders into `#root`

### Styling approach
- **Tailwind utility classes** for most layout/spacing/colors
- **Custom CSS** in a `<style>` block for: glassmorphism panels (`.glass-panel`, `.glass-card`), scroll-triggered fade-in (`.fade-in-up`), gradient text, custom scrollbar, image zoom on hover
- **Inline styles** used sparingly for dynamic background images and drag cursor
- **Dark mode only** — enforced via `<html class="dark">` and `darkMode: "class"` in Tailwind config
- Design tokens follow Material Design 3 naming (surface, on-surface, primary-fixed-dim, etc.)

### Key design patterns
- Glassmorphism UI: `backdrop-filter: blur(20px)` with semi-transparent backgrounds and subtle borders
- Scroll-triggered animations via Intersection Observer
- All images loaded from Google CDN (`lh3.googleusercontent.com/aida-public/...`) — NOT from local `/image/` directory
- "联系我" (Contact Me) button and footer links are non-functional stubs

## Notes

- The `/image/` directory contains `B.png` (793 KB) and `game.png` (38 MB) — these are **unreferenced** by the app and are likely leftover assets.
- React Router uses `MemoryRouter`, so there are no bookmarkable URLs or browser history support. Changing to `BrowserRouter` or `HashRouter` would require a server-side rewrite rule for SPA routing.
- The app uses React 18's `createRoot` API (not the legacy `ReactDOM.render`).
- Tailwind CDN with plugins (`forms, container-queries`) — these plugin names may be misspelled (`container-queries`), and the CDN may silently ignore them.
