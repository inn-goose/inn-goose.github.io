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
- **Layout overrides**: `layouts/` contains two theme overrides — a custom `alert` shortcode and a patched `icon` partial
- **Static assets**: Favicons and OG image in `static/`; author avatar in `assets/images/`
- **Deployment**: Push to `main` triggers `.github/workflows/gh-pages.yml` which builds and deploys to GitHub Pages

## Content

Content lives in the `content/` submodule. To update it:
```bash
cd content && git checkout main && git pull && cd -
```

Blog posts are in `content/blog/<slug>/index.md` with images alongside. Tags are used for taxonomy (e.g., `project` tag powers the Projects page).

## Known Issues (as of 2026-04-03)

### 1. CRITICAL: robots.txt points to example.com

`static/robots.txt` has `Sitemap: https://www.example.com/sitemap.xml` instead of `https://goose.sh/sitemap.xml`. This is the primary reason Google cannot discover the site's pages. The actual sitemap at `https://goose.sh/sitemap.xml` is correct (20 URLs), but nothing tells search engines where to find it.

**Additionally**, `enableRobotsTXT: false` in `hugo.yaml` is misplaced under `params:` (line 77) — this is a top-level Hugo key, so Hugo ignores it there. The Blowfish theme sets `enableRobotsTXT = true` at the top level in its own config, making the effective value `true`.

**Fix**: Delete `static/robots.txt` entirely. Add `enableRobotsTXT: true` as a top-level key in `config/_default/hugo.yaml` (not under `params:`). Blowfish's template auto-generates the correct sitemap URL via `{{ "sitemap.xml" | absURL }}` and blocks crawlers in non-production environments. Then register the site in Google Search Console and submit the sitemap.

### 2. CRITICAL: GitHub Actions workflow issues (.github/workflows/gh-pages.yml)

- **`peaceiris/actions-hugo@v2`** uses Node.js 16 (deprecated). v3 exists, but Hugo's official docs recommend installing Hugo directly via `curl`/`dpkg` with no third-party action dependency.
- **`hugo-version: "latest"` overrides `HUGO_VERSION: 0.150.0`** — the env var is defined but never used by the action. `"latest"` is non-reproducible and has known 404 failures (peaceiris/actions-hugo#664).
- **Missing `--cacheDir` flag** — the workflow saves/restores `${{ runner.temp }}/hugo_cache` but never passes `--cacheDir` to Hugo, so the cache steps do nothing.
- **`DART_SASS_VERSION: 1.92.1` is dead code** — defined but never installed. Blowfish doesn't use Sass.
- **Outdated action versions**: `actions/checkout@v5` (v6 is current), `actions/cache@v4` (v5 is current).
- **Missing `--baseURL` flag** — should pass `--baseURL "${{ steps.pages.outputs.base_url }}/"` for correctness.

**Fix**: Replace the workflow with Hugo's official recommended approach: direct Hugo install via `curl`/`dpkg`, pin `HUGO_VERSION`, add `--cacheDir` and `--baseURL` flags, remove `DART_SASS_VERSION`, upgrade action versions.

### 3. HIGH: Blowfish theme outdated (v2.91.0)

Current version declares `max = "0.151.0"` for Hugo compatibility. Local Hugo is v0.152.2 which exceeds this, producing `WARN Module "blowfish" is not compatible with this Hugo version`. Latest Blowfish is v2.101.0 (2026-03-30) supporting up to Hugo 0.159.1.

**Fix**: `cd themes/blowfish && git fetch --tags && git checkout v2.101.0`

### 4. MEDIUM: Firebase security rules likely too permissive

Blowfish's recommended Firestore rules (`allow read, write: if request.auth != null` on `{document=**}`) combined with anonymous auth allow any user to:
- Read, write, or delete any document in any collection
- Create arbitrary collections to exhaust free-tier quota
- Zero out or inflate all view/like counts

**Fix**:
- Tighten Firestore rules to only allow increment-by-1 on `views` and `likes` collections, deny everything else (including deletes)
- Restrict the API key in Google Cloud Console to HTTP referrers matching `goose.sh/*`
- Enable Firebase App Check with reCAPTCHA v3 so only the real site can make requests
- Set up billing alerts in GCP

### 5. LOW: Duplicate GA4 config (not causing issues)

`services.googleAnalytics.id` and `params.firebase.measurementId` both specify `G-4V51YVFN4Y`. No double-counting occurs — Blowfish only loads Firebase for Firestore (views/likes) and never loads `firebase-analytics.js`. The `measurementId` in the Firebase config is inert. The `services.googleAnalytics` block drives the actual gtag.js snippet.

## Improvements & Refactoring

### SEO & Discoverability

- Register site in [Google Search Console](https://search.google.com/search-console), verify ownership, and submit `https://goose.sh/sitemap.xml`
- After fixing robots.txt (see issue #1 above), use Search Console's "URL Inspection" to request indexing of the homepage
- Consider adding meta descriptions to blog posts for better search snippets

### GitHub Actions Workflow Modernization

Replace the current workflow with Hugo's official recommended approach:
- Install Hugo directly via `curl`/`dpkg` instead of `peaceiris/actions-hugo`
- Pin Hugo version explicitly using the `HUGO_VERSION` env var
- Add `--cacheDir "${{ runner.temp }}/hugo_cache"` to make caching work
- Add `--baseURL "${{ steps.pages.outputs.base_url }}/"` and `--gc` flags
- Upgrade to `actions/checkout@v6`, `actions/cache@v5`
- Remove unused `DART_SASS_VERSION` env var

### Theme Update

Update Blowfish from v2.91.0 to v2.101.0 for Hugo 0.152+ compatibility. Review the [Blowfish changelog](https://github.com/nunocoracao/blowfish/releases) for any breaking changes between v2.91.0 and v2.101.0 before upgrading. After updating, test locally with `hugo server -D`.

### Firebase Hardening

- Replace permissive Firestore rules with scoped rules that only allow increment-by-1 on `views/{docId}` and `likes/{docId}`, deny deletes, and block all other collections
- Restrict the Firebase API key in Google Cloud Console to `goose.sh/*` referrers
- Enable Firebase App Check with reCAPTCHA v3
- Set up GCP billing alerts to catch abuse

### Config Cleanup

- Move `enableRobotsTXT` from `params:` to top-level in `config/_default/hugo.yaml`
- Remove `params.firebase.measurementId` if not needed (analytics runs through gtag.js, not Firebase SDK)
- Remove the `# custom robots.txt` comment once the static file is deleted
