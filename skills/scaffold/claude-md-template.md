# CLAUDE.md Template for GTM Template Projects

Use this as the starting point for the project's `CLAUDE.md`. Fill in all `[PLACEHOLDER]` values.

```markdown
# [Template Name] - GTM Custom Template

## Project Overview
A Google Tag Manager custom template (.tpl) that [what it does]. [Why it exists / what problem it solves].

## Repository
https://github.com/VoxxyCreativeLab/template-[name]

## File Structure
\```
├── CLAUDE.md              # This file - project instructions for Claude Code
├── README.md              # User-facing documentation
├── template.tpl           # GTM template file (sandboxed JS, UI, permissions, tests)
├── metadata.yaml          # Template gallery metadata and versioning
├── .gitignore             # Git ignore rules
└── LICENSE                # [License type]
\```

## Key Technical Details

### Architecture
[Describe the architecture — does it use the injectScript pattern? Is logic entirely in sandboxed JS?]

### GTM Sandboxed JS APIs Used
[List the APIs: e.g., injectScript, setInWindow, logToConsole, sendPixel]

### Events Tracked
[List and describe the events this template tracks]

| Event | Description | Default |
|---|---|---|
| [event_name] | [What triggers it] | [ON/OFF] |

### DataLayer Push Format
\```javascript
{
  'event': '[event_name]',
  '[detail_key]': '[detail_value]'
}
\```

### Permissions Required
[List permissions and what they're for]

## CDN Hosting (if using injectScript)
If this template loads a helper script or config from jsDelivr CDN:
- **Dedicated repo per product** — e.g. `VoxxyCreativeLab/cdn-[product-name]`. NEVER share a CDN repo with other products (jsDelivr has one flat version namespace per repo — tags collide).
- **Use `@v1` (major version)** in CDN URLs — auto-resolves to latest `v1.x.x` tag
- **Always use `v` prefix** on tags: `v1.0.0`, `v1.1.0`, etc.
- **Never delete+recreate tags** — jsDelivr caches permanently. Bump version instead.
- **Never use `@main`** — 12h cache, purge API unreliable

### CRITICAL: Tagging & deployment rules
- **Every push to the CDN repo MUST include a new semver tag.** Pushing to `main` without tagging does NOTHING — jsDelivr `@v1` only resolves to tagged commits. Always run: `git tag v1.0.X && git push origin v1.0.X`
- **CRITICAL: Always `git fetch --tags` before assigning a version number.** Run `git tag -l "v1.*" | sort -V | tail -1` to find the ACTUAL latest tag from remote. Never assign a version based on stale local state. If the latest tag is higher than expected, STOP — another session has pushed. Reconcile first. **Why this rule exists:** In March 2026, two parallel Claude sessions each checked their own local/stale tag state. Session A pushed commits labeled v1.0.16–v1.0.19 without tagging them. Session B (seeing stale local tags, latest = v1.0.14) assigned v1.0.15 to the next feature, pushed on top of the v1.0.19 commits, and tagged it v1.0.15. The CDN ended up with tag v1.0.15 containing all v1.0.19-era code. jsDelivr served correct code only by coincidence.
- **`@v1` version-range cache takes up to 12h to update** after a new tag is pushed. The jsDelivr purge API does NOT clear this cache — it only purges the file cache. Be patient after tagging.
- **Backward compatibility within a major version is guaranteed by design.** A template deployed at v1.0.0 automatically receives v1.8.6 code via `@v1`. All changes within v1.x MUST be backward-compatible.
- **NEVER change `@v1` to `@v2` in a deployed template.** This breaks ALL existing GTM containers using that template. A major version bump requires every client to: (1) delete the old template, (2) re-import the new `.tpl` file, (3) re-publish their GTM container. Treat major version bumps as a migration event, not a simple update.

### Multi-repo sync (if using a separate CDN repo)
If this project edits code in a source repo and mirrors it to a separate CDN repo:
- **CDN tags are independent from source repo versions.** Never copy source version numbers to CDN tags.
- **Always derive the next CDN tag from `git tag` on the CDN repo**, not from CHANGELOG or memory.
- **Log every sync/skip in a CDN Sync Log** at the bottom of CHANGELOG.md.
- **Never store CDN tag numbers in memory files** — they go stale between sessions.

## Development Guidelines
- Template logic is in `template.tpl` using GTM sandboxed JS APIs only
- The `.tpl` format has specific sections: `___INFO___`, `___TEMPLATE_PARAMETERS___`, `___SANDBOXED_JS_FOR_WEB_TEMPLATE___`, `___WEB_PERMISSIONS___`, `___TESTS___`, `___NOTES___`
- `___WEB_PERMISSIONS___` must match the APIs actually used in the sandboxed JS
- Template parameters define the UI — test changes in GTM Template Editor
- Test by importing into GTM > Templates > Tag Templates > Import

## Testing Workflow
1. Import `template.tpl` into GTM Template Editor
2. Run built-in tests in the `___TESTS___` section
3. Preview GTM container on a test page
4. Verify in GTM debug panel that only correct events fire
```
