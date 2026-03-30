---
name: permissions
description: Generate and validate GTM template WEB_PERMISSIONS JSON blocks for sandboxed JS APIs. Use when building or reviewing the ___WEB_PERMISSIONS___ section of a .tpl file.
disable-model-invocation: false
---

# GTM Template Permissions

You are helping with the `___WEB_PERMISSIONS___` section of a GTM custom template `.tpl` file.

## Your Tasks

When asked to **generate permissions**:
1. Read the `___SANDBOXED_JS_FOR_WEB_TEMPLATE___` section
2. Identify every `require()` call
3. For each API, generate the correct permission JSON block from [[permissions-reference]]
4. Combine all blocks into a JSON array

When asked to **validate permissions**:
1. Read both the sandboxed JS and the `___WEB_PERMISSIONS___` section
2. Check that every `require()` has a matching permission
3. Check for unused permissions (declared but not used in code)
4. Verify all type codes, key names, and structure are correct
5. Report any mismatches

## Key Rules

- Every sandboxed API loaded via `require()` needs a corresponding permission block (except `JSON`, `Math`, `Object`, encoding/decoding APIs, type conversion APIs, and other permission-free APIs)
- The `access_globals` permission must list every window property the template reads, writes, or executes
- `setInWindow` with `overrideExisting: true` requires both `read: true` and `write: true` — GTM classifies this as a readwrite operation at runtime
- URL patterns in `inject_script` and `send_pixel` should use wildcards where appropriate
- `logging` should almost always use `"debug"` environment (not `"all"`)
- Permission type codes: `1` = String, `2` = List, `3` = Map, `8` = Boolean

Consult [[permissions-reference]] for the exact JSON structure of each permission type.

## Quick Example

Code:
```javascript
const injectScript = require('injectScript');
const setInWindow = require('setInWindow');
const logToConsole = require('logToConsole');
```

Requires these permission blocks: `inject_script`, `access_globals` (with read+write for the window property), and `logging`. The `JSON`, `makeString`, `makeNumber`, and similar utility APIs do NOT need permissions.

## Common Mistakes

- **Missing `read: true` on `setInWindow` with `overrideExisting: true`** — GTM treats this as a readwrite operation; omitting read causes a silent runtime permission error
- **Wildcard too broad in `inject_script`** — use `https://cdn.jsdelivr.net/gh/YourOrg/your-repo*` not `https://*`
- **Forgetting `callInWindow` permission** — if your helper script sets a callback that sandboxed JS calls via `callInWindow`, you need an `access_globals` entry with `execute: true` for that function name
- **Unused permissions left in** — GTM Gallery review flags permissions that don't match any `require()` call
