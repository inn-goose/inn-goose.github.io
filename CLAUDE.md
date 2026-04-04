# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal blog at [goose.sh](https://goose.sh) built with Hugo and the Blowfish theme, deployed to GitHub Pages.

## Commands

```bash
# Build the site
hugo

# Local dev server (includes drafts)
hugo server -D
# Serves at http://localhost:1313/

# Production build (used in CI)
hugo --minify
```

## Architecture

- **Static site generator**: Hugo with the [Blowfish](https://blowfish.page) theme
- **Git submodules**: Both `content/` (blog content from `inn-goose/content` repo) and `themes/blowfish/` are submodules — clone with `--recurse-submodules`
- **Config**: Single config file at `config/_default/hugo.yaml`
- **Layout overrides**: `layouts/` overrides two Blowfish templates to enable per-icon-type alert styling with dark/light mode support:
  - `partials/icon.html` — adds icon name as CSS class (e.g., `class="icon circle-info"`)
  - `shortcodes/alert.html` — adds `alert-card` + icon name classes, removes `shadow`
  - `assets/css/custom.css` — defines colors for `circle-info` (blue), `triangle-exclamation` (yellow), `fire` (red) in both light and dark modes
  - This is intentional — Blowfish's built-in `cardColor`/`iconColor` params don't support dark mode
- **Static assets**: Favicons and OG image in `static/`; author avatar in `assets/images/`
- **Deployment**: Push to `main` triggers `.github/workflows/gh-pages.yml` which builds and deploys to GitHub Pages

## Content

Content lives in the `content/` submodule. To update it:
```bash
cd content && git checkout main && git pull && cd -
```

Blog posts are in `content/blog/<slug>/index.md` with images alongside. Tags are used for taxonomy (e.g., `project` tag powers the Projects page).

## Known Issues (as of 2026-04-04)

### 1. LOW: Taxonomies override disables theme defaults (intentional)

`hugo.yaml` only defines `tag: tags` under `taxonomies:`, which disables `categories`, `authors`, and `series` from Blowfish defaults. This is intentional — content only uses tags.


## Improvements & Refactoring

### SEO & Discoverability

- Consider adding meta descriptions to blog posts for better search snippets

### Firebase Hardening (deferred — requires GCP billing)

- Restrict the Firebase API key in Google Cloud Console to `goose.sh/*` referrers
- Enable Firebase App Check with reCAPTCHA v3
- Set up GCP billing alerts

Not critical — Firestore rules are hardened and Spark plan has hard daily limits (50k reads, 20k writes).

### Config Notes

- `services.googleAnalytics.id` is required — Blowfish's `ga.html` reads from `site.Config.Services.GoogleAnalytics.ID`
- `params.firebase.measurementId` was removed (inert, analytics runs through gtag.js)

