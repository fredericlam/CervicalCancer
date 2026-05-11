# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev       # Start dev server on port 3002
npm run build     # Build for production (output: dist/)
npm run preview   # Preview production build on port 8080
```

No test framework is configured.

## Architecture

**Vue 3 + Vite** data visualization app for cancer epidemiology statistics. Each route is a self-contained visualization page.

### Stack
- **Vue 3** with Options API (not Composition API) in page components
- **D3.js** (v7 via npm + v5 loaded globally from `index.html`) for SVG visualizations
- **Vuex** store (mostly unused — minimal state)
- **Vue Router** with lazy-loaded page components
- **PrimeVue** with custom Noir/zinc dark theme
- **Bootstrap 5**, **Font Awesome**, **jQuery** loaded globally via `index.html`

### Key Pattern: Vue + D3 Integration
Each page component manages its own D3 visualization. Vue handles UI controls (filters, buttons, dropdowns) and reactive state; D3 renders to a `#graphic` DOM element directly. Data is fetched via `axios` from JSON/CSV files in `/public/data/`.

Typical page lifecycle:
1. `mounted()` → fetch data via axios → call D3 draw function
2. Vue watchers or event handlers trigger D3 redraw when filters change

### Routes → Pages
| Route | Component | Data Source |
|-------|-----------|-------------|
| `/` | `Cervix.vue` | `dataset.json` |
| `/thyroid` | `Thyroid.vue` | `dataset-thyroid-v3.json` |
| `/thyroid_age` | `Thyroid.vue` | age-specific variant |
| `/asr_hdi` | `asr-HDI.vue` | `hdi_2020_i_m.json`, CSV files |
| `/prostate_2023` | `2023_Prostate.vue` | `Prostate_2023.json` |
| `/cost` | `Cost.vue` | inline data |

### Data Files (`/public/data/`)
Country-level cancer statistics (incidence, mortality, ASR) and HDI data. Multiple thyroid dataset versions exist (`v1`, `v2`, `v3`) — `v3` is the current one used.

### Global JS in `index.html`
`index.html` loads D3 v5, d3-annotation, d3-legend, science.js, and jQuery globally before the Vue app. Some older page components may use the global D3 v5 instance rather than the npm D3 v7 import.

### Deployment
Deployed on Netlify (primary). `netlify.toml` sets build command to `npm run build`, publish dir to `dist/`, Node 20.
