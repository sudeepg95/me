# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

An interactive personal portfolio built with Astro 5, TypeScript, and Tailwind CSS. Deploys to `https://sudeepg95.github.io/me`. The defining feature is an adaptive graphics system that selects a renderer based on device capability.

## Commands

```bash
npm run dev      # Dev server (localhost)
npm run build    # Production build → dist/
npm run preview  # Preview production build
npm run format   # Prettier formatting
npm run clean    # Remove dist/ and node_modules/
```

## Architecture

### Adaptive Graphics System

The core architectural pattern is progressive renderer degradation. On page load, `GraphicsManager` (`src/types/graphics-manager.ts`) probes the device:

1. **WebGPU** — if supported and device is high-end
2. **WebGL** via the `ogl` library — mid-range fallback
3. **CSS animations** — fallback for mobile, low battery, `prefers-reduced-motion`

Detection factors: WebGPU API availability, `navigator.hardwareConcurrency`, battery level, mobile UA, reduced-motion media query.

The `adaptive-graphics.astro` component orchestrates renderer selection. Individual renderers are in `src/components/dumb/` (`webgpu-starfield.astro`, `webgl-starfield.astro`, `webgpu-snowfield.astro`, `webgpu-laserfield.astro`).

### Data Flow

Profile content lives in `src/data/user-profile.ts` as a typed object. It's accessed through the `UserProfileManager` class (`src/lib/user-profile-manager.ts`), which provides computed properties and enforces immutable updates via spread operators. Types are defined in `src/types/user-profile.ts`.

### Page Structure

`src/pages/index.astro` is the single page. It composes: `Header` → `Splash` (graphics) → `Intro` → `Skills` → `Footer`. Battery-aware font loading is handled in the page-level script.

### Path Alias

`~/` maps to `src/` (configured in `tsconfig.json`).

## Key Dependencies

- `ogl` — WebGL rendering library for the mid-tier starfield
- `@sudeepg95/legendary-cursor` — custom cursor effect component
- `astro-icon` + Iconify JSON sets (fa-brands, fa-solid, mdi, simple-icons)
- `tailwindcss-fluid-type` — responsive fluid typography

## Deployment

Configured for GitHub Pages via `astro.config.mjs`:
```js
site: "https://sudeepg95.github.io"
base: "/me"
```
