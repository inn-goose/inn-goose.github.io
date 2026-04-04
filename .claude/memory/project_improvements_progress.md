---
name: Repository improvements progress
description: Tracks which CLAUDE.md improvements have been completed and what remains
type: project
---

Improvement status as of 2026-04-04:

1. **robots.txt fix** — DONE. Committed by user. Deployed. Static file deleted, `enableRobotsTXT: true` added to top-level config. Google Search Console registered, sitemap submitted.

2. **GitHub Actions workflow** — DONE. Committed (1f72751). Replaced peaceiris action with direct Hugo install, pinned version, fixed caching with `--cacheDir`, added `--baseURL` and `--gc`, removed `DART_SASS_VERSION`, bumped action versions.

3. **Blowfish theme update** — IN PROGRESS. Submodule checked out to v2.101.0 but not yet committed. Blocked on content fix (double-paren links). See `project_blowfish_upgrade.md`.

4. **Firebase hardening** — TODO.

5. **Config cleanup** — TODO.

**How to apply:** Resume from item 3 once the content submodule is fixed.
