# Hamza Yousaf — Interactive 3D Portfolio

## Project Overview
- **Name**: Hamza Yousaf — Executive Director, Mentix Technologies
- **Goal**: A scroll-driven, live 3D portfolio presenting Hamza Yousaf's leadership roles,
  engineering work and platform projects with an agency-grade, hand-crafted feel.
- **Subject**: Executive Director at Mentix Technologies · 4 years experience (since 2022)

## Features (completed)
- **Live WebGL scene** (Three.js via importmap, no bundling) — noise-displaced core with custom
  GLSL (simplex/fbm/ridge), finite-difference normal recomputation, fresnel rim shell,
  wire lattice, instanced satellite nodes, particle field and horizon grid.
- **Scroll choreography** — a 6-phase `PHASES` table interpolated with smootherstep drives
  camera, core position/scale/spin, noise amplitude, shell, grid, particle spread, accent
  colour and bloom intensity. Each page section owns one phase.
- **Post-processing** — UnrealBloom + a custom pass (chromatic aberration, vignette, dither).
- **LITE mode** — auto-enabled by `?lite=1`, `prefers-reduced-motion`, or software-rasteriser
  detection (SwiftShader/llvmpipe via `WEBGL_debug_renderer_info`); skips post-processing and
  thins geometry.
- **Responsive 3D framing** — `computeLayout()` scales/offsets the object per breakpoint
  (`xScale`, `yScale`, `yShift`, `zPush`) so it never collides with the text column.
- **Interaction layer** — GSAP + ScrollTrigger + Lenis smooth scroll, per-character hero
  reveal, manifesto word wipe, animated stat counters, section-synced nav/HUD, project-hover
  accent recolouring of the 3D scene, custom cursor, click-to-copy email, live local clock.
- **Readability** — per-panel radial scrim behind copy, brightened ink tokens, text shadows.
- **Mobile** — thumb-reachable fixed bottom nav pill, stacked project rows, softened 3D layer,
  safe-area aware, zero horizontal overflow.
- **SEO** — JSON-LD `Person` schema (jobTitle, worksFor, sameAs).

## Content / Sections
| Phase | Route fragment | Content |
|---|---|---|
| 0 | `#index`   | Hero — name, role, stats (4+ yrs, 3 companies led, since 2022) |
| 1 | `#work`    | Manifesto intro |
| 2 | `#work`    | Projects: AUST AI-driven ODL platform, Redis server in C, Mentix Software & Academy, Senexa |
| 3 | `#craft`   | Capabilities — AI platforms, low-level systems, cloud (Oracle Cloud, AWS) |
| 4 | `#path`    | Timeline — Executive Director (Aug 2026→), Lead Architect AUST (Apr 2026→), Co-founder & CTO Senexa (2025–May 2026), Software Engineer (2022–2025) |
| 5 | `#contact` | Email + GitHub / LinkedIn / Fiverr |

## Functional Entry URIs
- `GET /` — the full single-page application (all sections are in-page anchors).
- `GET /?lite=1` — force low-power mode (no post-processing, reduced geometry).
- `GET /static/scene.js` — WebGL scene module.
- `GET /static/app.js` — interaction/UI layer.
- `GET /static/style.css` — design system + responsive rules.

Anchors: `#index`, `#work`, `#craft`, `#path`, `#contact`.

## Links
- **Email**: couthamza1@outlook.com
- **GitHub**: https://github.com/Hamza88i
- **LinkedIn**: https://www.linkedin.com/in/hamza-yousaf-38a8202bb/
- **Fiverr**: https://www.fiverr.com/hamzasardar557

## Data Architecture
- **Data models**: none — content is static and authored directly in `src/index.tsx`
  (`NAV`, `LINKS`, and the section markup). Scene choreography lives in the `PHASES`
  array in `public/static/scene.js`.
- **Storage services**: none required. No database, KV or R2 binding is used; there is no
  user-generated or persisted state.
- **Data flow**: Hono renders the HTML document at the edge → the browser loads Three.js from
  a CDN importmap → `scene.js` builds the WebGL scene and exposes `window.HYScene` →
  `app.js` drives scroll progress into `HYScene.setProgress()` / `setAccent()`.

## User Guide
1. Open the site and wait for the short preloader (it dismisses as soon as the scene is ready).
2. **Scroll** — the 3D object is bound to scroll position; every section has its own camera
   framing, colour and bloom.
3. **Click the nav** (top on desktop, bottom pill on mobile) to jump between sections.
4. **Hover a project row** to recolour the 3D scene with that project's accent.
5. **Click the email address** in Contact to copy it to the clipboard.
6. Append `?lite=1` to the URL on a low-powered device to disable post-processing.

## Tech Stack
Hono 4 · TypeScript · Vite · Cloudflare Pages · Three.js 0.169 (importmap) · GSAP 3 +
ScrollTrigger · Lenis · custom GLSL

## Development
```bash
npx vite build                      # build to dist/  (preferred over `npm run build`
                                    # while the dev server is running)
pm2 start ecosystem.config.cjs      # serve dist/ via wrangler pages dev on :3000
pm2 logs webapp --nostream          # inspect logs
curl http://localhost:3000          # smoke test
```

## Not Yet Implemented / Next Steps
- Not deployed to production yet — deploy path still to be chosen.
- No contact form (contact is mailto + profile links by design).
- Possible additions: per-project detail pages, an `og:image` social card, a WebGL-off static
  fallback hero image, and a Lighthouse/perf pass on real mobile hardware.

## Deployment

### Vercel (ready to deploy)
This project is preconfigured for Vercel. The original Hono app in `src/index.tsx` is
**unchanged** — Vercel deployment is enabled purely by three added files:

- `api/index.ts` — a Vercel Edge Function that imports the existing Hono `app` and wraps it
  with `hono/vercel`'s `handle()`.
- `vercel.json` — routes all non-asset requests to the Hono function and serves the
  `public/` folder (so `/static/*.js` and `/static/style.css` are served as static assets).
- `.vercelignore` — keeps the Cloudflare build artefacts out of the Vercel deploy.

**Deploy options:**

1. **Vercel dashboard (recommended)**
   - Push this repo to GitHub / GitLab / Bitbucket.
   - In Vercel → *Add New → Project* → import the repo.
   - Leave all build settings at their defaults (no build command / framework needed —
     `vercel.json` already sets `framework: null`, `buildCommand: null`,
     `outputDirectory: "public"`).
   - Click **Deploy**.

2. **Vercel CLI**
   ```bash
   npm i -g vercel
   vercel            # preview deploy
   vercel --prod     # production deploy
   ```

No environment variables, databases or bindings are required.

### Cloudflare Pages (original target — still supported)
- **Platform**: Cloudflare Pages
- **Note**: before deploying, set `name` in `wrangler.jsonc` to the project's
  `cloudflare_project_name`. No bindings are needed (no D1/KV/R2).

- **Last Updated**: 2026-08-26
