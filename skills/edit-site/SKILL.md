---
name: edit-site
description: Safely edit an existing Ivy + Lattice static site (built with the create-site/create-theme skills). Use this when adding pages, changing nav, swapping the palette, changing fonts, or adding sections/code to an existing site in the Ivy/Lattice ecosystem.
category: web
---

# edit-site — Edit an existing Ivy + Lattice site

You make **targeted, safe edits** to an existing static site built with the **Ivy** (classless semantic-HTML baseline) and **Lattice** (12-column layout utilities) CSS libraries and the **`<dark-mode-toggle>`** web component. You preserve the shared site anatomy, load order, and theming invariants so the site keeps working.

## When to use

- Adding a page, nav item, section, or code block to an existing Ivy/Lattice site.
- Changing the brand name/logo, swapping the palette, or changing fonts.
- Fixing or extending an existing site without breaking dark mode or accessibility.

## Ecosystem

- **Ivy** — classless CSS baseline. Docs: https://github.com/rlnorthcutt/ivy/tree/main/docs
- **Lattice** — 12-column layout utilities. Docs: https://github.com/rlnorthcutt/lattice/tree/main/docs
- **dark-mode-toggle** — web component for light/dark/auto theming. Repo: https://github.com/rlnorthcutt/dark-mode-toggle

## Workflow

1. **Identify the site root and confirm the stack** — find the folder containing the HTML pages. Confirm it uses Ivy/Lattice by looking for the CSS links (`ivy.full.min.css`, `lattice.full.min.css`) or the vendored files in the directory.
2. **Read before editing** — read the existing page structure and `theme.css` first. Understand the header/nav, the prepaint script position, the load order, and which tokens the theme overrides.
3. **Make targeted edits** — preserve the invariants below.
4. **Adding a new page** — copy the closest existing template/page, then wire the nav links on **ALL** pages (not just the new one).
5. **Editing the theme** — only change the `-light`/`-dark` variant tokens on `:root`. Never touch applied tokens.
6. **Re-validate** — run `assets/theme-checklist.md` after any change.

## Invariants to preserve (never break these)

- **Prepaint script position** — the prepaint script must stay **before** the CSS `<link>` tags in `<head>` (prevents flash of wrong theme).
- **Load order** — Ivy first, then Lattice, then the theme/site CSS. Never reorder.
- **`aria-current="page"`** — exactly one nav link per page carries `aria-current="page"`, matching the current page.
- **Skip link** — keep the skip link as the first element in `<body>`, targeting `#content`.
- **Script tags at end of body** — `dark-mode-toggle.min.js` stays at the very end of `<body>`.
- **Keep `<dark-mode-toggle>` in the nav** — the toggle `<li>` must remain in the header nav on every page.
- **Never edit the vendored min.css files** — `ivy.full.min.css` and `lattice.full.min.css` are library builds; do not modify them. Put all site CSS in `theme.css`/`site.css`.
- **Never override applied tokens** — only the `-light`/`-dark` variant tokens on `:root`. Overriding `--color-bg`/`--color-text`/etc. directly gets clobbered by the theme switcher and breaks dark mode.

## Common edits cookbook

### Add a nav item
Edit the `<nav aria-label="Primary"><ul>` in the header of **every page**. Add `<li><a href="page.html">Label</a></li>` before the `<dark-mode-toggle>` `<li>`. Set `aria-current="page"` on the new link only on the new page.

### Add a page
Copy the closest existing template/page (e.g. `page-doc.html` for a docs page). Update the `<title>`, the nav `aria-current` on all pages, and the content. Add the new page's link to the nav on **all** pages.

### Change brand name/logo
Edit the brand `<a href="./">` block in the header of every page: replace the `<span>Site Name</span>` text and the SVG placeholder with the new name/logo.

### Swap the palette
Edit only the `-light`/`-dark` variant tokens in `theme.css` on `:root`. Do not add applied-token overrides. Prefer `color-mix(in oklab, ...)` for derived tints.

### Add a section with anchor + sidebar entry
On a doc page: add `<section id="new-section" aria-labelledby="new-section-title">` inside the `<article class="span lg-span-9">`, then add a matching `<li><a href="#new-section">…</a></li>` to the "On this page" sidebar `<aside>`. The `[id]{scroll-margin-top:5.5rem}` rule keeps the anchor clear of the sticky header.

### Add code blocks
Use `<pre><code>…</code></pre>` inside a section. Escape `<` and `>` in the code sample (e.g. `&lt;script&gt;`).

### Change fonts
Add `--font-sans` / `--font-mono` overrides in `theme.css` on `:root`, e.g. `--font-sans: "YourFont", var(--font-sans);`. Load the webfont (e.g. via `<link>` in `<head>` or `@import`) before it is used.

## Key token knowledge (condensed)

Ivy uses a **two-tier token system**:
1. **Variant tokens** — `--color-X-light` and `--color-X-dark` (the source values).
2. **Applied tokens** — `--color-bg`, `--color-text`, `--color-muted`, `--color-link`, `--color-primary`, `--color-primary-text`, `--color-secondary`, `--color-secondary-text`, `--color-border`, `--color-surface`, `--surface-2`, `--surface-3`, `--shadow-sm`, `--shadow-md` (remapped by theme state).

**Override only the `-light`/`-dark` variants on `:root` — never the applied tokens.** Theme files are plain unlayered CSS loaded after the libraries, so they win without `!important`. Optional Lattice tokens: `--lat-container-width`, `--lat-space`, `--lat-gap-x`, `--lat-gap-y`. Type/rhythm tokens: `--font-sans`, `--font-mono`, `--base-size`, `--lh`, `--measure`, `--radius`, `--m`, `--fs-sm`.

> For the full theme-authoring guide (default token block, worked example, color-mix guidance), see the **`create-theme`** skill.

## Reference docs

### Ivy — https://github.com/rlnorthcutt/ivy/tree/main/docs
- `index.html` — install / theme / customize / token guide.
- `ivy-core.html` & `ivy-extra.html` — element demos.

Key facts:
- Ivy = **classless semantic-HTML baseline**.
- Extra components: `.card`, `.callout` (+ `data-tone`), `.btn-group`, buttons via `data-variant="primary|secondary|outline|ghost"` and `data-size="sm|lg"`, `.eyebrow`, `.kicker`, `.muted`, `.divider`, `.surface-2`/`.surface-3`, `[data-badge]`, `[data-tooltip]`.

### Lattice — https://github.com/rlnorthcutt/lattice/tree/main/docs
- `index.html` — quickstart / token guide.
- `core.html`, `extra.html` — layout demos.
- `examples.html` — layout patterns.

Key facts:
- Lattice = **12-column layout utilities**; put `class="lattice"` on `<body>`.
- `.grid`, `.col-N` / `.span-N` with `sm-`/`md-`/`lg-`/`xl-`/`xxl-` responsive prefixes.
- Breakpoints: sm 40rem, md 48rem, lg 64rem, xl 80rem, xxl 96rem.
- Spacing utilities: `.p/.m/.gap-0..5` plus directional variants `px/py/pt/pb/pl/pr/mx/my/mt/mb/ml/mr`.

## Validation

After any change, run through `assets/theme-checklist.md`. At minimum confirm:
- Both light and dark palettes render correctly.
- No applied-token overrides in the theme (only `-light`/`-dark` variants).
- Toggle persists across reload (`localStorage` `dm:theme`).
- No flash of wrong theme on load (prepaint script present before CSS).
- Toggle present in the nav on every page.
- `aria-current="page"` on the active nav link, skip link present, `scroll-margin-top` set.