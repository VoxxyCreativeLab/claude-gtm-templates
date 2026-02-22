# GTM Template Permissions Reference

## Permission JSON Structure

Each permission in `___WEB_PERMISSIONS___` follows this structure:

```json
{
  "instance": {
    "key": {
      "publicId": "<permission_name>",
      "versionId": "1"
    },
    "param": [
      {
        "key": "<param_key>",
        "value": {
          "type": <type_code>,
          ...
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

## Type Codes

| Code | Type   | Value Property |
|------|--------|----------------|
| `1`  | String | `"string": "value"` |
| `2`  | List   | `"listItem": [...]` |
| `3`  | Map    | `"mapKey": [...], "mapValue": [...]` |
| `8`  | Boolean| `"boolean": true/false` |

---

## Permission Blocks by API

### `logging` (for `logToConsole`)

```json
{
  "instance": {
    "key": {
      "publicId": "logging",
      "versionId": "1"
    },
    "param": [
      {
        "key": "environments",
        "value": {
          "type": 1,
          "string": "debug"
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

Use `"debug"` for preview-only logging. Use `"all"` to log in production (rarely needed).

---

### `access_globals` (for `setInWindow`, `copyFromWindow`, `callInWindow`, `createQueue`, `createArgumentsQueue`, `aliasInWindow`)

Each window property needs a map entry with `key`, `read`, `write`, and `execute` flags.

```json
{
  "instance": {
    "key": {
      "publicId": "access_globals",
      "versionId": "1"
    },
    "param": [
      {
        "key": "keys",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 3,
              "mapKey": [
                {"type": 1, "string": "key"},
                {"type": 1, "string": "read"},
                {"type": 1, "string": "write"},
                {"type": 1, "string": "execute"}
              ],
              "mapValue": [
                {"type": 1, "string": "_myPropertyName"},
                {"type": 8, "boolean": false},
                {"type": 8, "boolean": true},
                {"type": 8, "boolean": false}
              ]
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

**Flag meanings:**
- `read: true` — needed for `copyFromWindow` or `createQueue`/`createArgumentsQueue`
- `write: true` — needed for `setInWindow` or `createQueue`/`createArgumentsQueue`
- `execute: true` — needed for `callInWindow`

**To add multiple properties**, add more map objects to the `listItem` array.

---

### `inject_script` (for `injectScript`)

```json
{
  "instance": {
    "key": {
      "publicId": "inject_script",
      "versionId": "1"
    },
    "param": [
      {
        "key": "urls",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 1,
              "string": "https://cdn.example.com/script.js"
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

**Wildcard patterns:** Use `*` at the end to match URL prefixes:
- `"https://cdn.jsdelivr.net/gh/MyOrg/cdn*"` — matches any path under this CDN
- `"https://example.com/scripts/*"` — matches any file in the scripts directory

---

### `send_pixel` (for `sendPixel`)

```json
{
  "instance": {
    "key": {
      "publicId": "send_pixel",
      "versionId": "1"
    },
    "param": [
      {
        "key": "allowedUrls",
        "value": {
          "type": 1,
          "string": "specific"
        }
      },
      {
        "key": "urls",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 1,
              "string": "https://collect.example.com/*"
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `get_cookies` (for `getCookieValues`)

```json
{
  "instance": {
    "key": {
      "publicId": "get_cookies",
      "versionId": "1"
    },
    "param": [
      {
        "key": "cookieAccess",
        "value": {
          "type": 1,
          "string": "specific"
        }
      },
      {
        "key": "cookieNames",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 1,
              "string": "_ga"
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `set_cookies` (for `setCookie`)

```json
{
  "instance": {
    "key": {
      "publicId": "set_cookies",
      "versionId": "1"
    },
    "param": [
      {
        "key": "allowedCookies",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 3,
              "mapKey": [
                {"type": 1, "string": "name"},
                {"type": 1, "string": "domain"},
                {"type": 1, "string": "path"},
                {"type": 1, "string": "secure"},
                {"type": 1, "string": "session"}
              ],
              "mapValue": [
                {"type": 1, "string": "_my_cookie"},
                {"type": 1, "string": "*"},
                {"type": 1, "string": "*"},
                {"type": 1, "string": "any"},
                {"type": 1, "string": "any"}
              ]
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `get_url` (for `getUrl`, `getQueryParameters`)

```json
{
  "instance": {
    "key": {
      "publicId": "get_url",
      "versionId": "1"
    },
    "param": [
      {
        "key": "urlParts",
        "value": {
          "type": 1,
          "string": "specific"
        }
      },
      {
        "key": "queriesAllowed",
        "value": {
          "type": 1,
          "string": "specific"
        }
      },
      {
        "key": "queryKeys",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 1,
              "string": "utm_source"
            },
            {
              "type": 1,
              "string": "utm_medium"
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

For full URL access (all components), use `"string": "any"` for `urlParts`.

---

### `get_referrer` (for `getReferrerUrl`, `getReferrerQueryParameters`)

Same structure as `get_url` but with `publicId: "get_referrer"`.

---

### `read_data_layer` (for `copyFromDataLayer`)

```json
{
  "instance": {
    "key": {
      "publicId": "read_data_layer",
      "versionId": "1"
    },
    "param": [
      {
        "key": "allowedKeys",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 3,
              "mapKey": [
                {"type": 1, "string": "key"},
                {"type": 1, "string": "read"},
                {"type": 1, "string": "write"}
              ],
              "mapValue": [
                {"type": 1, "string": "event"},
                {"type": 8, "boolean": true},
                {"type": 8, "boolean": false}
              ]
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `access_local_storage` (for `localStorage`)

```json
{
  "instance": {
    "key": {
      "publicId": "access_local_storage",
      "versionId": "1"
    },
    "param": [
      {
        "key": "keys",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 3,
              "mapKey": [
                {"type": 1, "string": "key"},
                {"type": 1, "string": "read"},
                {"type": 1, "string": "write"}
              ],
              "mapValue": [
                {"type": 1, "string": "myTemplate_clientId"},
                {"type": 8, "boolean": true},
                {"type": 8, "boolean": true}
              ]
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `access_consent` (for `isConsentGranted`, `addConsentListener`, `setDefaultConsentState`, `updateConsentState`)

```json
{
  "instance": {
    "key": {
      "publicId": "access_consent",
      "versionId": "1"
    },
    "param": [
      {
        "key": "consentTypes",
        "value": {
          "type": 2,
          "listItem": [
            {
              "type": 3,
              "mapKey": [
                {"type": 1, "string": "consentType"},
                {"type": 1, "string": "read"},
                {"type": 1, "string": "write"}
              ],
              "mapValue": [
                {"type": 1, "string": "ad_storage"},
                {"type": 8, "boolean": true},
                {"type": 8, "boolean": false}
              ]
            },
            {
              "type": 3,
              "mapKey": [
                {"type": 1, "string": "consentType"},
                {"type": 1, "string": "read"},
                {"type": 1, "string": "write"}
              ],
              "mapValue": [
                {"type": 1, "string": "analytics_storage"},
                {"type": 8, "boolean": true},
                {"type": 8, "boolean": false}
              ]
            }
          ]
        }
      }
    ]
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

Use `read: true` for `isConsentGranted` and `addConsentListener`. Use `write: true` for `setDefaultConsentState` and `updateConsentState`.

---

### `read_container_data` (for `getContainerVersion`)

```json
{
  "instance": {
    "key": {
      "publicId": "read_container_data",
      "versionId": "1"
    },
    "param": []
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `read_event_metadata` (for `addEventCallback`)

```json
{
  "instance": {
    "key": {
      "publicId": "read_event_metadata",
      "versionId": "1"
    },
    "param": []
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `read_title` (for `readTitle`)

```json
{
  "instance": {
    "key": {
      "publicId": "read_title",
      "versionId": "1"
    },
    "param": []
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `read_character_set` (for `readCharacterSet`)

```json
{
  "instance": {
    "key": {
      "publicId": "read_character_set",
      "versionId": "1"
    },
    "param": []
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

### `inject_hidden_iframe` (for `injectHiddenIframe`)

Same structure as `inject_script` but with `publicId: "inject_hidden_iframe"`.

---

### `access_template_storage` (for `templateStorage`)

```json
{
  "instance": {
    "key": {
      "publicId": "access_template_storage",
      "versionId": "1"
    },
    "param": []
  },
  "clientAnnotations": {
    "isEditedByUser": true
  },
  "isRequired": true
}
```

---

## APIs That Do NOT Need Permissions

These APIs can be used freely without a corresponding `___WEB_PERMISSIONS___` entry:

- `JSON` (parse, stringify)
- `Math` (abs, floor, ceil, round, max, min, pow, sqrt)
- `Object` (keys, values, entries, freeze, delete)
- `encodeUri`, `encodeUriComponent`, `decodeUri`, `decodeUriComponent`
- `toBase64`, `fromBase64`
- `makeString`, `makeNumber`, `makeInteger`
- `makeTableMap`
- `getType`
- `generateRandom`
- `getTimestampMillis`
- `callLater`
- `sha256`
- `parseUrl`
- `queryPermission`

## Matching require() Calls to Permissions

| `require()` Call | Permission `publicId` |
|---|---|
| `require('injectScript')` | `inject_script` |
| `require('setInWindow')` | `access_globals` |
| `require('copyFromWindow')` | `access_globals` |
| `require('callInWindow')` | `access_globals` |
| `require('createQueue')` | `access_globals` |
| `require('createArgumentsQueue')` | `access_globals` |
| `require('aliasInWindow')` | `access_globals` |
| `require('logToConsole')` | `logging` |
| `require('sendPixel')` | `send_pixel` |
| `require('getCookieValues')` | `get_cookies` |
| `require('setCookie')` | `set_cookies` |
| `require('getUrl')` | `get_url` |
| `require('getQueryParameters')` | `get_url` |
| `require('getReferrerUrl')` | `get_referrer` |
| `require('getReferrerQueryParameters')` | `get_referrer` |
| `require('copyFromDataLayer')` | `read_data_layer` |
| `require('gtagSet')` | `write_data_layer` |
| `require('localStorage')` | `access_local_storage` |
| `require('templateStorage')` | `access_template_storage` |
| `require('isConsentGranted')` | `access_consent` |
| `require('addConsentListener')` | `access_consent` |
| `require('setDefaultConsentState')` | `access_consent` |
| `require('updateConsentState')` | `access_consent` |
| `require('getContainerVersion')` | `read_container_data` |
| `require('addEventCallback')` | `read_event_metadata` |
| `require('readTitle')` | `read_title` |
| `require('readCharacterSet')` | `read_character_set` |
| `require('injectHiddenIframe')` | `inject_hidden_iframe` |
