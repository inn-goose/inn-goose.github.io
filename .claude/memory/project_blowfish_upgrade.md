---
name: Blowfish theme upgrade in progress
description: Blowfish v2.91.0→v2.101.0 upgrade blocked by double-paren links in content submodule
type: project
---

Blowfish theme submodule already checked out to v2.101.0 in themes/blowfish.

Build fails because v2.101.0's render-link.html is stricter — it rejects double-parenthesized markdown links like `[text]((url))`.

**Affected file:** `content/blog/experiments-2-misconfigured-arduino-pins/index.md` lines 14 and 176 have `((https://...))` and `((/blog/...))` link targets. Line 195 is fine.

**Why:** `urls.Parse` in Hugo chokes on the leading `(` in the URL. Old Blowfish was lenient; new version is not.

**How to apply:** After user fixes the content submodule, run `hugo --minify --gc` to verify the build passes, then commit the updated theme submodule pointer in the parent repo.
