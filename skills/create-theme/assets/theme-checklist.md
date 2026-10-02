# Theme QA Checklist

Run through this checklist after building or changing a site-theme site. Tick every box before considering it done.

## Palettes & contrast

- [ ] **Light palette renders correctly** — toggle to light and verify all surfaces, text, borders, and links look right.
- [ ] **Dark palette renders correctly** — toggle to dark and verify the same.
- [ ] **Contrast is sufficient** — body text vs. background, muted text vs. background, and primary/secondary text vs. their button backgrounds all meet WCAG AA (4.5:1 for normal text, 3:1 for large text/UI).
- [ ] **Links are distinguishable** from body text in both palettes.
- [ ] **Focus states are visible** in both palettes.

## Token correctness

- [ ] **No applied-token overrides** — the theme only overrides `-light`/`-dark` variant tokens on `:root`. It does NOT set `--color-bg`, `--color-text`, `--color-muted`, `--color-link`, `--color-primary`, `--color-primary-text`, `--color-secondary`, `--color-secondary-text`, `--color-border`, `--color-surface`, `--surface-2`, `--surface-3`, `--shadow-sm`, or `--shadow-md` directly.
- [ ] **Both `-light` and `-dark` variants are set** for every color you override (no palette left at defaults unintentionally).
- [ ] **Semantic tokens** (`--info`, `--success`, `--warn`, `--danger`) are set if used.

## Dark mode toggle

- [ ] **Toggle persists across reload** — flip the theme, reload, and the choice sticks (`localStorage` key `dm:theme`).
- [ ] **No flash on load** — the prepaint script is present BEFORE the CSS links on every page, so the correct theme is applied on first paint.
- [ ] **Toggle is in the nav on every page** — `<li><dark-mode-toggle></dark-mode-toggle></li>` in the header.
- [ ] **Component script loads** — `<script src="dark-mode-toggle.min.js" defer></script>` (plain script, NOT `type="module"`) at the end of `<body>`.
- [ ] **Cross-tab sync works** — changing the theme in one tab updates others (BroadcastChannel `dm:theme`).
- [ ] **`themechange`/`change` events** fire and any listeners behave.

## Accessibility & structure

- [ ] **Skip link present** — first element in `<body>`, targets `#content`.
- [ ] **`aria-current="page"`** on the active nav link on every page.
- [ ] **`scroll-margin-top` set** — `[id]{scroll-margin-top:5.5rem;}` so anchored sections clear the sticky header.
- [ ] **Semantic landmarks** — `header[role="banner"]`, `<main id="content">`, `<footer>`.
- [ ] **Nav has an `aria-label`** (e.g. "Primary").
- [ ] **Images/icons** are decorative or have alt text / `aria-hidden`.
- [ ] **Keyboard navigation** — all links/buttons reachable and focusable; toggle operable by keyboard.

## Build & assets

- [ ] **Load order correct** — Ivy first, then Lattice, then theme/site CSS.
- [ ] **Theme file is unlayered** and loaded after the libraries (so it wins without `!important`).
- [ ] **Vendored assets present** if self-hosting (`ivy.full.min.css`, `lattice.full.min.css`, `dark-mode-toggle.min.js`).
- [ ] **No console errors** on load in both palettes.