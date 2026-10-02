---
name: create-theme
description: Create a light/dark CSS-variable theme (theme.css) for an Ivy + Lattice site. Use this when authoring or re-theming the color palette, type, or spacing of a site in the Ivy/Lattice ecosystem, or when you need to fill in the theme-template.css.
category: web
---

# create-theme — Create a site theme

You author a **`theme.css`** — a light/dark CSS-variable palette — for an existing or new **Ivy + Lattice** site. You gather brand colors/fonts, fill in the theme template, and validate the result against the QA checklist. You do **not** scaffold pages (see the `create-site` skill for that) — you make the site look right.

## When to use

- Creating a theme for a brand-new Ivy/Lattice site.
- Re-theming an existing Ivy/Lattice site (changing colors, type, spacing).
- Adding **light/dark mode** with a persistent, flash-free toggle.
- Filling in `theme-template.css` with a brand palette.

## Ecosystem

- **Ivy** — classless CSS baseline. Semantic HTML gets styled automatically. Layers: `@layer ivy-base, ivy-extra;`. Docs: https://github.com/rlnorthcutt/ivy/tree/main/docs
- **Lattice** — 12-column layout utilities. Docs: https://github.com/rlnorthcutt/lattice/tree/main/docs
- **dark-mode-toggle** — web component for light/dark/auto theming. Repo: https://github.com/rlnorthcutt/dark-mode-toggle

## Workflow

1. **Gather brand colors/fonts** — primary, secondary, link, background, text, surface, border, and any semantic status colors (info/success/warn/danger). Also note any brand font families.
2. **Fill the theme template** — copy `templates/theme-template.css` to `theme.css` (or edit an existing one) and fill in the `-light`/`-dark` variant tokens. See **Theme creation guide** below.
3. **Validate** — run the QA checklist in `assets/theme-checklist.md` and confirm both palettes, no flash, and persistence.

## Theme creation guide (critical)

Ivy uses a **two-tier token system**:

1. **Variant tokens** — `--color-X-light` and `--color-X-dark` for every color. These are the *source* values.
2. **Applied tokens** — `--color-bg`, `--color-text`, `--color-muted`, `--color-link`, `--color-primary`, `--color-primary-text`, `--color-secondary`, `--color-secondary-text`, `--color-border`, `--color-surface`, `--surface-2`, `--surface-3`, `--shadow-sm`, `--shadow-md`. These are *remapped* by theme state (light/dark) to the matching variant.

### The cardinal rule

**Override the `-light`/`-dark` variants on `:root` — NEVER the applied tokens directly.** The theme switcher overwrites the applied tokens when dark mode activates, so any direct override of `--color-bg`/`--color-text`/etc. will be clobbered (and will break dark mode). Always set the `-light` and `-dark` variants and let the theme state remap them.

### Default token block (baseline)

This is the full Ivy default. Use it as the reference baseline when creating a theme:

```css
:root {
  --color-bg-light:#f8faf8; --color-text-light:#141a17; --color-muted-light:#5b6a63;
  --color-link-light:#1a5f43; --color-primary-light:#1e6f47; --color-primary-text-light:#ffffff;
  --color-secondary-light:#3f4a54; --color-secondary-text-light:#ffffff;
  --color-border-light:#dde5e0; --color-surface-light:#ffffff;
  --surface-2-light:#f1f5f2; --surface-3-light:#e9efeb;
  --color-bg-dark:#0b100e; --color-text-dark:#e6ecea; --color-muted-dark:#9aa9a3;
  --color-link-dark:#79dcae; --color-primary-dark:#5fd39b; --color-primary-text-dark:#04231a;
  --color-secondary-dark:#a9b7c1; --color-secondary-text-dark:#101418;
  --color-border-dark:#232b27; --color-surface-dark:#121815;
  --surface-2-dark:#161d1a; --surface-3-dark:#1c2521;
  --font-sans:"Inter", ui-sans-serif, system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,Cantarell,Noto Sans,Helvetica Neue,Arial,sans-serif;
  --font-mono:ui-monospace,SFMono-Regular,Menlo,Monaco,Consolas,"Liberation Mono",monospace;
  --base-size:16px; --lh:1.6; --measure:64rem; --radius:.375rem; --m:1rem;
  --info:#60a5fa; --success:#22c55e; --warn:#f59e0b; --danger:#ef4444;
}
```

### How theme files work

- Theme files are **plain unlayered CSS loaded after the libraries**. Ivy's layers are `@layer ivy-base, ivy-extra;` — unlayered CSS wins over layered CSS **without** needing `!important`.
- Optional **Lattice tokens**: `--lat-container-width`, `--lat-space`, `--lat-gap-x`, `--lat-gap-y`.
- **Type/rhythm tokens**: `--font-sans`, `--font-mono`, `--base-size:16px`, `--lh:1.6`, `--measure:64rem`, `--radius:.375rem`, `--m:1rem` (spacing derives from `--m`), `--fs-sm:.875rem`.

### Derived tints with color-mix

Prefer `color-mix(in oklab, ...)` for tints derived from your palette rather than hand-picking every shade. It keeps a theme cohesive and lets you tint from a single source color.

### Worked example (arbor theme)

```css
:root {
  --color-primary-light:        #114232;
  --color-primary-text-light:   #ffffff;
  --color-secondary-light:      #87A922;
  --color-secondary-text-light: #0e1a00;
  --color-link-light:           #87A922;
  --color-muted-light:          color-mix(in oklab, #114232 28%, #9ca3af);
  --color-border-light:         color-mix(in oklab, #FCDC2A 30%, transparent);
  --color-surface-light:        color-mix(in oklab, #F7F6BB 10%, #ffffff);
  --surface-2-light:            color-mix(in oklab, #F7F6BB 22%, #ffffff);
  --surface-3-light:            color-mix(in oklab, #FCDC2A 14%, #ffffff);
  --color-primary-dark:         #2F6B3F;
  --color-primary-text-dark:    #FFF6C0;
  --color-secondary-dark:       #7FB77E;
  --color-link-dark:            #F7C85C;
  --color-muted-dark:           color-mix(in oklab, #7FB77E 38%, #a1aab8);
  --color-border-dark:          color-mix(in oklab, #2F6B3F 30%, transparent);
  --color-surface-dark:         color-mix(in oklab, #2F6B3F 13%, #111418);
  --surface-2-dark:             color-mix(in oklab, #2F6B3F 20%, #18202a);
  --surface-3-dark:             color-mix(in oklab, #7FB77E 14%, #202a36);
}
```

## Dark mode toggle interaction

`<dark-mode-toggle>` is a web component. It reads/writes the theme and syncs across tabs. Your theme must cooperate with it:

- The component (and the prepaint script) set `data-theme="dark"` on `<html>` (or add a `dark` class) when dark mode activates, which remaps the applied tokens to the `-dark` variants. This is why you only override the `-light`/`-dark` variants.
- Persists to `localStorage` key **`dm:theme`**; syncs across tabs via **`BroadcastChannel("dm:theme")`**; emits **`change`** and **`themechange`** events.
- Attributes: `theme="auto|light|dark"`, `strategy="attr|class"`, `root`, `dark-class`, `persist="off"`, `label`, `disabled`.
- Shadow-DOM appearance vars: `--track-width`, `--track-height`, `--track-bg-light`, `--track-bg-dark`, `--thumb-bg`, `--thumb-bg-dark`, `--icon-sun-color`, `--icon-moon-color`. SVG slots: `<svg slot="sun">` and `<svg slot="moon">`.

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

### Lattice — https://github.com/rlnorthcutt/lattice/tree/main/docs
- `index.html` — quickstart / token guide.
- `core.html`, `extra.html` — layout demos.
- `examples.html` — layout patterns.

## Validation

After authoring, run through `assets/theme-checklist.md`. At minimum confirm:
- Both light and dark palettes render correctly.
- No applied-token overrides in the theme (only `-light`/`-dark` variants).
- Toggle persists across reload (`localStorage` `dm:theme`).
- No flash of wrong theme on load (prepaint script present before CSS).
- Toggle present in the nav on every page.
- `aria-current="page"` on the active nav link, skip link present, `scroll-margin-top` set.
