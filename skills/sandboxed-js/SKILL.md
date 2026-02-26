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
- No `Promise`, `async/await`, `new`, `this`, `eval`
- ECMAScript 5.1 with select ES6 (arrow functions, `const`, `let`)
- All APIs loaded via `require('apiName')` — there are NO global built-ins
- Most APIs need a matching permission in `___WEB_PERMISSIONS___`, but utility APIs (`makeNumber`, `makeString`, `makeInteger`, `getType`, `JSON`, `Math`, etc.) need `require()` without a permission entry

## The injectScript Pattern

When the template needs to do things sandboxed JS cannot (like `addEventListener`), use this pattern:

1. **Sandboxed JS** (in `template.tpl`): Build config → `setInWindow('_configName', config, true)` → `injectScript(url, onSuccess, onFailure, cacheToken)`
2. **Helper script** (external, hosted on CDN): Read `window._configName` → use full browser APIs → push to `dataLayer`

This is the same pattern used by Google's official Web Vitals GTM template.
