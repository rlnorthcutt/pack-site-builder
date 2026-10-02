---
name: create-site
description: Create a complete multi-page static site from user-provided content (docs, product info, markdown/notes/outline) using the Ivy + Lattice CSS libraries and the <dark-mode-toggle> web component. Use this when scaffolding a docs site, product/landing page, or any multi-page static site in the Ivy/Lattice ecosystem.
category: web
---

# create-site — Create a site from content

You scaffold a **complete multi-page static site** from user-provided content (docs, product info, markdown/notes/outline) styled with the **Ivy** (classless semantic-HTML baseline) and **Lattice** (12-column layout utilities) CSS libraries, and wired with the **`<dark-mode-toggle>`** web component for light/dark theming. You build pages from the templates in `templates/`, create a theme, cross-link the nav, and validate the result.

## When to use

- Building a **docs site**, **product/landing page**, or any **multi-page static site** in the Ivy/Lattice ecosystem.
- Turning user-provided content (markdown, notes, an outline, product copy) into a real site.
- Scaffolding a new page that must match the shared site anatomy (header/nav/toggle/footer).

## Ecosystem

- **Ivy** — classless CSS baseline. Semantic HTML gets styled automatically. Layers: `@layer ivy-base, ivy-extra;`. Docs: https://github.com/rlnorthcutt/ivy/tree/main/docs
- **Lattice** — 12-column layout utilities. Docs: https://github.com/rlnorthcutt/lattice/tree/main/docs
- **dark-mode-toggle** — web component for light/dark/auto theming. Repo: https://github.com/rlnorthcutt/dark-mode-toggle

## Workflow

1. **Clarify the page inventory from the content** — read the provided content and decide the pages: a landing/index page, one or more doc pages, and an examples index. Group the content into sections that map to pages.
2. **Scaffold the directory** — create the site folder and bring in the libraries (CDN or vendored). See **Install: CDN + vendoring** below.
3. **Build pages from templates** — copy the relevant page template(s) from `templates/` and fill in the content. See **Content mapping** below.
4. **Create the theme** — copy `templates/theme-template.css` to `theme.css` and fill in the `-light`/`-dark` variant tokens. Follow the `create-theme` skill's guidance (or the guide embedded in the template).
5. **Cross-link the nav** — set `aria-current="page"` on the active nav link on every page.
6. **Validate** — run the QA checklist in `assets/theme-checklist.md` and confirm both palettes, no flash, and persistence.

## Install: CDN + vendoring

Load order matters: **Ivy first, then Lattice, then your site/theme CSS.**

```html
<!-- Ivy (classless baseline) -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/rlnorthcutt/ivy/dist/ivy.full.min.css">
<!-- Lattice (layout utilities) -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/rlnorthcutt/lattice/dist/lattice.full.min.css">
<!-- Your theme (unlayered, overrides the libraries) -->
<link rel="stylesheet" href="theme.css">
```

**Vendoring** (self-hosting, for offline/private use): download the dist files and reference them locally.

```bash
# Ivy
curl -L -o ivy.full.min.css https://cdn.jsdelivr.net/gh/rlnorthcutt/ivy/dist/ivy.full.min.css
# Lattice
curl -L -o lattice.full.min.css https://cdn.jsdelivr.net/gh/rlnorthcutt/lattice/dist/lattice.full.min.css
# dark-mode-toggle (web component) — vendor from the @main dist
curl -L -o dark-mode-toggle.min.js https://cdn.jsdelivr.net/gh/rlnorthcutt/dark-mode-toggle@main/dist/dark-mode-toggle.min.js
```

Then reference the local files:

```html
<link rel="stylesheet" href="ivy.full.min.css">
<link rel="stylesheet" href="lattice.full.min.css">
<link rel="stylesheet" href="theme.css">
...
<script src="dark-mode-toggle.min.js" defer></script>
```

> **dark-mode-toggle is an IIFE, not an ES module.** Load it as a plain `<script src="dark-mode-toggle.min.js" defer></script>` (or at the end of `<body>`), **NOT** `type="module"`. The templates reference it as a locally vendored file with a comment noting the source.

## Page templates

Templates live in `templates/`. Copy the one that fits, then customize.

| Template | When to use |
| --- | --- |
| `page-landing.html` | Home/landing/index page — hero, CTA, feature grid. |
| `page-doc.html` | Docs/reference page — sidebar "On this page" nav + article sections. |
| `page-examples.html` | Examples/demos index — grid of `.card` items. |
| `partial-header.html` | Reusable header fragment (brand + nav + toggle). |
| `partial-footer.html` | Reusable footer fragment. |

### Shared page anatomy

Every page follows the same structure:

1. **Skip link** — first element in `<body>`, links to `#content`.
2. **Sticky header** `role="banner"` — brand + nav + `<dark-mode-toggle>`.
3. **`<main id="content">`** — the page content.
4. **Footer**.
5. **Scripts** — `dark-mode-toggle.min.js` at the end of `<body>`.

Rules:
- `aria-current="page"` on the active nav link.
- `[id]{scroll-margin-top:5.5rem;}` so anchored sections clear the sticky header.
- Hero/page-title fluid type:

```css
.hero h1{font-size:clamp(2.75rem,7vw,4.75rem);line-height:1.02;letter-spacing:-.025em}
.hero .lede{font-size:1.2rem;color:var(--color-muted);max-width:42rem}
```

Sticky header needs a small site.css addition (include in your theme/site.css):

```css
header[role="banner"]{position:sticky;top:0;z-index:20;background:color-mix(in oklab, var(--color-bg) 92%, transparent);backdrop-filter:blur(10px);}
[id]{scroll-margin-top:5.5rem;}
```

### Header fragment (from Ivy docs, adapt)

```html
<header role="banner">
  <div class="container d-flex items-center justify-between gap-3">
    <a href="./" style="display:flex;align-items:center;gap:.5rem;text-decoration:none;color:var(--color-text);font-weight:600;letter-spacing:-.01em">
      <svg width="22" height="22" viewBox="0 0 24 24" aria-hidden="true" fill="currentColor" style="color:var(--color-primary);flex:none"><!-- brand icon --></svg>
      <span>Site Name</span>
    </a>
    <nav aria-label="Primary">
      <ul>
        <li><a href="index.html" aria-current="page">Home</a></li>
        <li><a href="docs.html">Docs</a></li>
        <li><a href="https://github.com/…" target="_blank" rel="noopener">GitHub</a></li>
        <li><dark-mode-toggle></dark-mode-toggle></li>
      </ul>
    </nav>
  </div>
</header>
```

## Content mapping

Turn the user's content into pages:

- **Landing page** (`page-landing.html`) — distill the content into a **hero** (kicker + fluid `h1` + lede + CTA buttons) and a **feature grid** of `.card` items (one card per key feature/benefit). Use `data-variant="primary|outline"` and `data-size="lg"` on CTA buttons.
- **Doc pages** (`page-doc.html`) — one page per doc topic. Use the **"On this page" sidebar** layout: a 12-col grid with `aside.span lg-span-3` (sticky section nav) and `article.span lg-span-9` (the content). Each section gets an `id` matching a sidebar link. Use `.callout` with `data-tone="info|success|warn|danger"` for tips/warnings, and `<pre><code>` for code blocks.
- **Examples index** (`page-examples.html`) — a responsive grid of `.card` items, one per example/demo, each with a footer link.

### Ivy/Lattice component vocabulary

- **Ivy extra components**: `.card`, `.callout` (+ `data-tone`), `.btn-group`, buttons via `data-variant="primary|secondary|outline|ghost"` and `data-size="sm|lg"`, `.eyebrow`, `.kicker`, `.muted`, `.divider`, `.surface-2`/`.surface-3`, `[data-badge]`, `[data-tooltip]`.
- **Lattice layout**: put `class="lattice"` on `<body>`. Use `.grid`, `.col-N` / `.span-N` with `sm-`/`md-`/`lg-`/`xl-`/`xxl-` responsive prefixes.
- **Breakpoints**: sm 40rem, md 48rem, lg 64rem, xl 80rem, xxl 96rem.
- **Spacing utilities**: `.p/.m/.gap-0..5` plus directional variants `px/py/pt/pb/pl/pr/mx/my/mt/mb/ml/mr`.

## Dark mode toggle

`<dark-mode-toggle>` is a web component. It reads/writes the theme and syncs across tabs.

### Attributes

- `theme="auto|light|dark"` — current theme (default `auto`).
- `strategy="attr|class"` — how dark mode is applied: `attr` sets `data-theme="dark"` on `root`; `class` adds a `dark` class.
- `root` — selector for the element to theme (default `:root`/`html`).
- `dark-class` — class name used when `strategy="class"`.
- `persist="off"` — disable persistence (default persists).
- `label` — accessible label.
- `disabled` — disable the toggle.

### Storage & sync

- Persists to `localStorage` key **`dm:theme`**.
- Syncs across tabs via **`BroadcastChannel("dm:theme")`**.
- Emits **`change`** and **`themechange`** events.

### Prepaint script (must go BEFORE the CSS links)

Place this **before** the `<link>` stylesheets to avoid a flash of the wrong theme on load:

```html
<script>
(() => {
  const KEY = 'dm:theme';
  const root = document.documentElement;
  const prefersDark = matchMedia('(prefers-color-scheme: dark)').matches;
  let mode;
  try { mode = localStorage.getItem(KEY); } catch {}
  if (!mode) mode = (root.getAttribute('data-theme')==='dark'||root.classList.contains('dark'))?'dark':null;
  if (!mode) mode = prefersDark ? 'dark' : 'light';
  root.style.colorScheme = mode;
  if (mode === 'dark') root.setAttribute('data-theme','dark');
})();
</script>
```

A copy-paste-able version lives in `assets/dark-mode-prepaint.html`.

### Wiring it in

- Toggle goes in the header nav: `<li><dark-mode-toggle></dark-mode-toggle></li>`.
- Component script loads at the end of `<body>`:
  ```html
  <script src="dark-mode-toggle.min.js" defer></script>
  ```
  (or vendored from https://github.com/rlnorthcutt/dark-mode-toggle).

## Reference docs

### Ivy — https://github.com/rlnorthcutt/ivy/tree/main/docs
- `index.html` — install / theme / customize / token guide.
- `ivy-core.html` & `ivy-extra.html` — element demos.

Key facts:
- Ivy = **classless semantic-HTML baseline** (no classes needed for base styling).
- Extra components: `.card`, `.callout` (+ `data-tone`), `.btn-group`, buttons via `data-variant="primary|secondary|outline|ghost"` and `data-size="sm|lg"`, `.eyebrow`, `.kicker`, `.muted`, `.divider`, `.surface-2`/`.surface-3`, `[data-badge]`, `[data-tooltip]`.

### Lattice — https://github.com/rlnorthcutt/lattice/tree/main/docs
- `index.html` — quickstart / token guide.
- `core.html`, `extra.html` — layout demos.
- `examples.html` — layout patterns.

Key facts:
- Lattice = **12-column layout utilities**.
- Put `class="lattice"` on `<body>`.
- `.grid`, `.col-N` / `.span-N` with `sm-`/`md-`/`lg-`/`xl-`/`xxl-` responsive prefixes.
- Breakpoints: sm 40rem, md 48rem, lg 64rem, xl 80rem, xxl 96rem.
- Spacing utilities: `.p/.m/.gap-0..5` plus directional variants `px/py/pt/pb/pl/pr/mx/my/mt/mb/ml/mr`.

## Validation

After building, run through `assets/theme-checklist.md`. At minimum confirm:
- Both light and dark palettes render correctly.
- No applied-token overrides in the theme (only `-light`/`-dark` variants).
- Toggle persists across reload (`localStorage` `dm:theme`).
- No flash of wrong theme on load (prepaint script present before CSS).
- Toggle present in the nav on every page.
- `aria-current="page"` on the active nav link, skip link present, `scroll-margin-top` set.