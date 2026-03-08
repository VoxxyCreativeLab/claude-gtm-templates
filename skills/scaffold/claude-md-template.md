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
