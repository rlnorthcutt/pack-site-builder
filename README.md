# pack-site-builder

Build themed static sites with the Ivy + Lattice CSS libraries and dark-mode-toggle: create themes, scaffold sites from content, and edit existing sites safely.

A pack in the Open Pack Format (OPF). A pack is a manifest plus embedded files, containing one or more items: skills, tools, data, routines, or artifacts. Packs install, update, and remove with zero native harness code.

See `spec/opf-spec-v1.md` in the opf-core repository for the format.

## What this pack does

This pack bundles three skills for building and maintaining **static sites** in the **Ivy + Lattice** ecosystem, wired with the **`<dark-mode-toggle>`** web component for light/dark theming. Together they cover the full lifecycle: create a theme, scaffold a site from content, and safely edit an existing site.

## Skills

| Skill | Purpose |
| --- | --- |
| `create-theme` | Author a `theme.css` — a light/dark CSS-variable palette — for an Ivy + Lattice site. Gather brand colors/fonts, fill in the theme template, and validate against the QA checklist. |
| `create-site` | Scaffold a complete multi-page static site from user-provided content (docs, product info, markdown/notes/outline) using the Ivy + Lattice libraries and `<dark-mode-toggle>`. |
| `edit-site` | Make targeted, safe edits to an existing Ivy + Lattice site: add pages, change nav, swap the palette, change fonts, or add sections/code — preserving the shared site anatomy, load order, and theming invariants. |

Each skill ships its own `templates/` and `assets/` (page templates, a theme template, a dark-mode prepaint snippet, and a QA checklist).

## Ecosystem

These skills target the following open-source projects:

- **Ivy** — a classless CSS baseline: semantic HTML gets styled automatically. Docs: https://github.com/rlnorthcutt/ivy/tree/main/docs
- **Lattice** — 12-column layout utilities. Docs: https://github.com/rlnorthcutt/lattice/tree/main/docs
- **dark-mode-toggle** — a web component for light/dark/auto theming. Repo: https://github.com/rlnorthcutt/dark-mode-toggle

## Contents

- `manifest.json` — the OPF v1 manifest.
- `skills/` — the three bundled skills (`create-theme`, `create-site`, `edit-site`).
- `data/` — data shipped with this pack.
- `.github/workflows/scan.yml`, `.gitlab-ci.yml` — CI scanning, already wired to opf-core's validator and scan template. No setup needed.
- `hooks/pre-commit` — a local pre-commit security scan (fast feedback, not a substitute for CI — see below).

## Install

Install this pack with the `pack-install` skill, or `scripts/install-pack.sh` for a harness that runs scripts but not skills — never by copying this folder by hand. If `manifest.json` declares `dependencies`, only `pack-install` resolves the closure (installing each one first, in order, per spec Section 4.5); `install-pack.sh` alone does not — it installs only this pack and prints a `NOTE` about the skipped dependencies. Either way requires opf-core's tooling to be reachable by your harness (a checkout, with `OPF_CORE` set if your harness needs it) — see opf-core's own README for that one-time setup. If a dependency's skills don't show up after install, run `pack-doctor` to diagnose why.

## Usage

Once installed, the three skills are available to the harness by name:

1. **`create-theme`** — when authoring or re-theming the color palette, type, or spacing of a site.
2. **`create-site`** — when scaffolding a docs site, product/landing page, or any multi-page static site from content.
3. **`edit-site`** — when adding pages, changing nav, swapping the palette, changing fonts, or adding sections/code to an existing site.

The skills reference each other by name (e.g. `create-site` points to `create-theme` for theme-authoring guidance), so they work together as a set.

## Validate

This pack ships no `scripts/` of its own — `validate-pack.sh` lives in opf-core. With an opf-core checkout available (`OPF_CORE` pointing at it):

```
"$OPF_CORE/scripts/validate-pack.sh" .
```

## Enable the pre-commit hook

Not automatic — git never reads `hooks/` on its own. Once per clone:

```
git config core.hooksPath hooks
```

This runs `validate-pack.sh` plus, where installed, `gitleaks` and the curated semgrep ruleset before each commit, refusing it on an error-class finding. It degrades gracefully (a NOTE, not a failure) for whatever isn't installed or configured, and it's bypassed by `git commit --no-verify`. It's convenience and fast feedback, not the enforced gate — that's CI. Run `scripts/pack-doctor.sh` after cloning this pack if you're not sure whether this is set.

## Maintained by

rlnorthcutt