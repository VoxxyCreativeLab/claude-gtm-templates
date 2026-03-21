---
name: sandboxed-js
description: GTM sandboxed JavaScript API reference, constraints, and common patterns like injectScript. Use when working on .tpl template files or building GTM custom templates.
disable-model-invocation: false
---

# GTM Sandboxed JavaScript Reference

You have access to a comprehensive API reference for all GTM sandboxed JavaScript APIs. Consult [api-reference.md](api-reference.md) for:

- All available APIs with signatures, parameters, and examples
- Permission requirements for each API
- The `data` object and `gtmOnSuccess`/`gtmOnFailure` callbacks
- Sandbox constraints (what you cannot do)
- Common implementation patterns

## Key Constraints to Remember

- No direct `window`, `document`, or DOM access
- No `addEventListener` — use the `injectScript` pattern for event listeners
- No `setTimeout` — use `callLater()` instead
- No `try`/`catch`/`finally` — sandboxed APIs return `undefined` on error instead of throwing
- No `Promise`, `async/await`, `new`, `this`, `eval`
- ECMAScript 5.1 with select ES6 (arrow functions, `const`, `let`)
- All APIs loaded via `require('apiName')` — there are NO global built-ins
- Most APIs need a matching permission in `___WEB_PERMISSIONS___`, but utility APIs (`makeNumber`, `makeString`, `makeInteger`, `getType`, `JSON`, `Math`, etc.) need `require()` without a permission entry

## The injectScript Pattern

When the template needs to do things sandboxed JS cannot (like `addEventListener`), use this pattern:

1. **Sandboxed JS** (in `template.tpl`): Build config → `setInWindow('_configName', config, true)` → `injectScript(url, onSuccess, onFailure, cacheToken)`
2. **Helper script** (external, hosted on CDN): Read `window._configName` → use full browser APIs → push to `dataLayer`

This is the same pattern used by Google's official Web Vitals GTM template.

## CDN Hosting Rules (jsDelivr)

When hosting helper scripts, config files, or other assets on jsDelivr via GitHub:

### One product per repo — MANDATORY
jsDelivr has **one flat version namespace per GitHub repo**. All git tags are global — there is no per-directory or per-product scoping. If multiple products share a repo, their tags collide and `@v1`-style version ranges break.

**NEVER put multiple products in a shared CDN repo.** Each product that needs independent versioning MUST have its own dedicated GitHub repo.

```
WRONG:  VoxxyCreativeLab/cdn → calendly/, wistia/, como-banner/  (tags collide)
RIGHT:  VoxxyCreativeLab/cdn-calendly    → calendly-listener.js
        VoxxyCreativeLab/cdn-wistia      → wistia-listener.js
        VoxxyCreativeLab/cdn-como-banner → como-global.json, vcl-como-banner.js
```

### Version tagging rules
- **Always use `v` prefix:** `v1.0.0`, `v1.1.0` (jsDelivr strips the single `v` for semver matching)
- **Use `@v1` (major only) in CDN URLs** — auto-resolves to latest `v1.x.x` tag
- **Never delete+recreate tags** — jsDelivr caches permanently. Bump the version instead.
- **Never use `@main`** — 12h cache, purge API unreliable
- **Tag prefixes like `product-v1.0.0` do NOT work** — jsDelivr only strips one `v`, the rest becomes invalid semver

### Multi-Repo CDN Sync (source repo + CDN repo)

When a project has a **source repo** (where code is edited) and a separate **CDN repo** (mirrors code to jsDelivr), these additional rules apply:

#### Independent version sequences — CRITICAL
The source repo and CDN repo have **completely independent version numbers**. The source repo increments on every meaningful change (code, docs, config). The CDN repo tag increments ONLY when the script file changes. A source repo at v1.0.27 does NOT mean the CDN should be at v1.0.27.

**NEVER derive a CDN tag number from the source repo version, CHANGELOG, or memory files.** Always derive it from `git fetch --tags && git tag -l "v1.*" | sort -V | tail -1` on the CDN repo.

#### Commit & push workflow
When pushing changes:

1. **Fetch remote tags on BOTH repos** before any versioning decision
2. **Check if the CDN-synced file changed:** `git diff HEAD src/[script].js`
   - **If YES (Path A):** Commit source repo → copy file to CDN repo → commit CDN repo → create next sequential tag → push both → log the sync
   - **If NO (Path B):** Commit and push source repo only. Do NOT touch the CDN repo. Log the skip.
3. **Log every sync decision** in a CDN Sync Log (in CHANGELOG or a dedicated file): source version, CDN tag (or "—" for skipped), commit hash, brief note

#### Version state is NEVER cached
Do not store CDN tag numbers in memory files, session context, or project state documents. They go stale between sessions. The only source of truth is `git tag` on the CDN repo after fetching from remote.

#### Tag integrity checks
After fetching tags, verify:
- Tags are sequential (no unexpected gaps)
- No two tags point to the same commit (indicates a duplicate/erroneous tag)
- The latest tag points to the most recent commit on main

If any check fails, **STOP and report the anomaly** before proceeding.
