# AGENTS.md

Portfolio site for "Tupã Levi" / Serrated Portfolio / Earth Guardians BR. Static HTML + vanilla JS + Three.js + GSAP, all loaded from CDN. No build system, no npm, no tests, no linter.

## Quick Facts

| Aspect            | Value                                                                       |
| ----------------- | --------------------------------------------------------------------------- |
| Type              | Static multi-page HTML site                                                 |
| Pages             | 5 self-contained HTML files at repo root                                    |
| Dependencies      | None installed locally; CDN-only (cdnjs + Google Fonts)                     |
| Build / Test      | None — open `index.html` in a browser                                       |
| Server            | None required; no backend                                                   |
| Package files     | None (no `package.json`, `Makefile`, CI configs)                           |

## File Map

```
.
├── index.html          # 715 LOC — Main "Serrated Portfolio" landing page
├── calendar.html       # 455 LOC — Cyber Calendar (LocalStorage, month/week views)
├── egbr.html           # 1126 LOC — Earth Guardians BR page, 3D textured globe
├── sos-eg.html         # 362 LOC — SOS Águas da Prata collab page, 3D icosahedron
├── portifolio2.html    # 362 LOC — Older "Carbonized Monolith" portfolio iteration
└── public/
    ├── bhumisparsha-shop.png
    ├── calendar.png
    ├── egbr.png
    ├── psiu.png
    ├── sos.png
    ├── technosutra.png
    ├── github.png
    └── textures/
        ├── earth-blue-marble.jpg   # albedo map for egbr.html globe
        ├── earth-topology.png      # bump map for egbr.html globe
        └── earth-waterbodies.png   # specular map for egbr.html globe
```

`index.html` is the entry point. Each subsidiary page is reached via the project list on `index.html`.

## Running / Previewing

There is **no build step**. To preview locally:

```bash
# Anything that serves the directory works. Examples:
python3 -m http.server 8000
# or
npx serve .
```

Open `http://localhost:8000/index.html`.

> **Gotcha:** Opening `index.html` directly via `file://` works for most pages, but `egbr.html` loads textures via absolute paths like `/public/textures/earth-blue-marble.jpg`. A local server is required for that page to render the globe correctly.

## CDN Pinning (do not bump casually)

All pages use these exact CDN versions:

- `three.min.js` — `r128` via cdnjs
- `gsap.min.js` — `3.12.2` via cdnjs
- `ScrollTrigger.min.js` — `gsap 3.12.2` via cdnjs (only `egbr.html`, `sos-eg.html`)
- Google Fonts: `Space Mono`, `IBM Plex Mono`, `Inter`, `JetBrains Mono`

`gsap.registerPlugin(ScrollTrigger)` is required at the top of any script block that uses ScrollTrigger — both `egbr.html` and `sos-eg.html` do this.

## Per-Page Architecture

Each HTML file is fully **self-contained**: inline `<style>` block, inline `<script>` block, no shared CSS or JS. Treat each page as an isolated artifact.

### `index.html` — Main portfolio

- Sections: `#hero` (Three.js particle ring), `#work` (project list grid), `#activism` (tech stack posters), `<footer>` CTA.
- Hero animation: 8000-point particle system that toggles between circle and random scatter on load (see `initVitreous` / `triggerReconstruction`).
- Glitch text effect on `#glitch-text` runs on a `setInterval` of 150ms.
- Hover-preview follows the cursor via `mousemove` handlers using inline `onmouseenter="showImage(event, 'imgN')"` — see `project-item` blocks.
- "Bhumisparsha Shop" was added as project 01/06 (recent commits `09c3929` and `6c87136`). The spelling is **Bhumisparsha** (not Bhumisparsa) — the corrected version is on `master`.
- `mailto:` link points to `earth.tupa@gmail.com`.
- Project entries link to:
  - `https://shop.bhumisparshaschool.org` (item 1)
  - `https://hautlys.github.io/projects` (item 2)
  - `https://technosutra.bhumisparshaschool.org/home` (item 3)
  - `sos-eg.html` overlay → external Wix (`aguasdapratasos.wixstudio.com/2025`) and `sos-eg.html` (item 4)
  - `egbr.html` overlay → `egbr.html` and external Wix (`earthguardiansbr.wixsite.com/earth`) (item 5)
  - `calendar.html` (item 6)

### `calendar.html` — Cyber Calendar

- Pure JS state machine in a single `<script>` block.
- LocalStorage key: **`monolith_events`** (JSON-encoded).
- Views: `month` and `week` toggled via button; navigation via `changeMonth(-1|1)`.
- Mock OAuth2 flow: `#login-btn` click flips `state.isConnected` after 1.2s timer; `logout()` reverses it.
- No real authentication — purely cosmetic auth overlay.
- Default seed events when no localStorage: `SYSTEM RECOVERY` and `DATA OVERHAUL` for today.

### `egbr.html` — Earth Guardians BR

- Largest page (1126 LOC). Three.js wireframe/textured Earth globe with starfield.
- Loads **3 textures** with absolute paths `/public/textures/earth-{blue-marble,topology,waterbodies}.{jpg,png}` — must be served, not opened as file.
- 2 lights: ambient + directional + green `PointLight(0x00ff85)` rim light.
- Uses ScrollTrigger (`gsap.registerPlugin(ScrollTrigger)` at top of script).
- Language: `pt-BR`. External links go to `earthguardians.org`, Instagram handle `earthguardians_br`, and contact `TUPA@EARTHGUARDIANS.ORG` (uppercase by convention here).

### `sos-eg.html` — SOS Águas da Prata × EG

- Three.js wireframe icosahedron + particle sphere layered effect.
- Custom white circle `#cursor` (cursor hidden via `body { cursor: none }`).
- ScrollTrigger usage similar to `egbr.html`.

### `portifolio2.html` — Carbonized Monolith (older)

- Older grid-based layout iteration. Uses Inter + JetBrains Mono. Not currently linked from `index.html` — kept as a reference / alt-portfolio.

## Known Sections (index.html)

In execution order:

| Section id      | Purpose                                      |
| --------------- | -------------------------------------------- |
| `#hero`         | Three.js particle ring + glitched nameplate  |
| `#work`         | Project list (6 brutalist cards)             |
| `#activism`     | Tech stack posters                           |
| `#github`       | Live GitHub activity feed + top repos grid   |
| `<footer>`      | Connect CTA + Version overlay wrappers       |

The `#github` section fetches `/users/hautlys/events/public`, `/users/hautlys/repos?sort=pushed&per_page=6&type=owner`, and per-repo `/languages`. State lives in `localStorage` keys `gh_events_hautlys`, `gh_repos_hautlys`, `gh_langs_<full-name>`, each with a 5-minute TTL (`CACHE_TTL_MS`). Refresh-on-visibility-change logic skips the cache only when stale.

## Code Patterns and Conventions

- **No external CSS or JS files.** Everything is inline `<style>` and inline `<script>`.
- **Theme palette is per-page**, not shared. Each page defines its own `:root` CSS variables - do not assume values from one page apply to another.
  - `index.html`: `--green: #00FF85`, `--bg: #000000`, font Space Mono.
  - `calendar.html`: `--accent: #00FF85`, `--muted: #1a1a1a`, font IBM Plex Mono.
  - `egbr.html`: `--obsidian: #08080a`, `--tectonic-white: #f0f0f0`, `--accent: #00ff85`, Inter/JetBrains Mono.
  - `sos-eg.html`: `--amoled: #000000`, `--silver: #e0e0e0`, `--aqueous: #00f2ff`, Inter/JetBrains Mono.
- **Sections** are `<section>` elements with `min-height: 100vh` (most pages) - design consistency follows this convention.
- **Cursor styles** are NOT set globally with `cursor: crosshair` on `*`. Each page scopes the crosshair to `body`/decorative surfaces only and applies `cursor: pointer` to actual navigable elements (`a`, `button`, `.project-item`, `.repo-card`, `.activity-repo`, `.version-btn`).
  - `sos-eg.html` is the exception: its `body { cursor: none }` is now gated behind `@media (pointer: fine) and (hover: hover)` so touch users keep the system cursor.
- **Accessibility primitives added in every page** (introduced in the UI/UX pass):
  - `@media (prefers-reduced-motion: reduce)` kills all animations and hides `<canvas>` decorations.
  - `a:focus-visible, button:focus-visible, [tabindex]:focus-visible { outline: 2px dashed }`.
  - `.skip-link` is the first element after `<body>`; it slides into view on `:focus` and links to the page's main content anchor.
  - Meta tags: `description`, `theme-color`, `color-scheme="dark"` are present in `<head>`.
  - Decorative elements (canvases, marquees, scanlines, noise overlays) carry `aria-hidden="true"`.
- **Brutalist/terminal aesthetic** repeated across pages: clip-path serrated edges (`--serrated` polygon variable), hard borders, monospace uppercase labels, scanline overlays (linear-gradient with `background-size: 100% 4px`).
- **Layout grids:** mix of 12-col skeleton (`portifolio2.html`), auto-fit poster grid (`index.html`), 7-col calendar grid (`calendar.html`).
- **IntersectionObserver** used in `index.html` for `.poster`/`.project-item`/`.repo-card`/`.activity-row` reveal-on-scroll and to pause the glitch-text effect when `#hero` is offscreen.
- **Color conventions for Three.js scenes:** green (`#00FF85` / `0x00ff85`) is the consistent accent.
- **Escape-to-close** is wired on `index.html` overlays (`#version-overlay`, `#sos-version-overlay`) and on calendar's auth overlay context.
- **Arrow-key calendar nav**: month-grid cells are focusable (Tab to enter grid, arrow keys to move, Enter/Space to add event).

## Naming and Style

- JS variables: camelCase (`showImage`, `moveImage`, `currentDate`, `triggerReconstruction`).
- Constants: UPPER_SNAKE (`PARTICLE_COUNT`, `circlePositions`, `randomPositions`).
- Three.js objects use module global pattern (`let scene, camera, renderer`).
- CSS classes: kebab-case / single-word (`project-item`, `cta-btn`, `manifesto-grid`, `day-cell`).
- IDs: kebab-case (`vitreous-canvas`, `auth-overlay`, `version-overlay`).
- Section IDs: semantic (`hero`, `work`, `activism`).
- Mixed whitespace across files (some pages tab-indent, some space-indent). When editing, **match the file's existing indentation exactly**.

## Testing

There is **no test framework, linter, or formatter** in this repo. Verification is manual — open affected pages in a browser and confirm:

1. No console errors on load.
2. Three.js scenes render (or fall back gracefully on resize).
3. LocalStorage round-trip in `calendar.html` works (add an event, reload, verify it persists).
4. External project links (`window.open`/`window.location.href`) point to intended destinations.

## Common Gotchas

- **Bhumisparsha spelling.** Recent commits intentionally corrected earlier typo "Bhumisparsa". Don't "fix" this back — verify the canonical form is **Bhumisparsha**.
- **`email.tupa` vs `earth.tupa`.** `index.html` footer CTA uses `earth.tupa@gmail.com`; `egbr.html` shows `TUPA@EARTHGUARDIANS.ORG`. Don't conflate.
- **Image paths use `/public/...`** in `index.html:375,382,389,396,403,410` (root-relative). Most pages use them via inline `<img src="public/...">` — works under both `file://` and a server, but `egbr.html` uses Three.js texture loader with absolute `/public/textures/...` paths which **require a server**.
- **Wix legacy URLs.** Several "old version" links go to `*.wixsite.com` / `*.wixstudio.com`. These are external and out of our control — do not rewrite.
- **Mock auth in `calendar.html`.** The "OAuth2" flow is fake. Don't try to wire it to anything real.
- **No module bundler / no `import`.** Everything is global-script. Adding a `<script type="module">` would break scripts that rely on `THREE` being a global.
- **GSAP animations mutate `BufferAttribute` arrays directly** (see `index.html:669` `gsap.to(posAttr.array, …)`). The wrapped numeric keys `[i3]` are intentional template-keyed GSAP tween syntax — preserve this exactly.
- **No image preprocessor.** Project preview PNGs in `public/` are referenced by name. If a new project is added, drop the PNG into `public/` and reference it directly.
- **Two footer/CTA versions.** `index.html` contains TWO version-selection overlays (`#version-overlay` for Earth Guardians, `#sos-version-overlay` for SOS). When editing, only modify the matching overlay.
- **Inter font weight matrix differs per page** — `egbr.html` requests `weight@900` only, `sos-eg.html` requests `100;300;900`. Don't assume a weight is available without checking the per-page `<link>`.

## Editing Workflow

1. Identify which page (each is isolated — changes don't cross files).
2. Inspect the entire file before editing (some are 1000+ LOC).
3. Match existing indentation precisely (tabs vs 4-space vs 2-space varies).
4. Preserve the inline-style + `<style>` + `<script>` triple pattern; don't extract to external files unless the user explicitly asks for a build step.
5. After editing, load the page in a browser and check DevTools console.

## Things Explicitly NOT in This Repo

- No `package.json` / `node_modules`
- No build tool (Webpack, Vite, esbuild)
- No test framework (Jest, Vitest, Playwright)
- No linter / formatter (ESLint, Prettier)
- No CI config
- No `.env`, no backend, no API routes
- No markdown content files (this AGENTS.md aside)

If asked to add any of the above, confirm intent first — the project is intentionally zero-dep.
