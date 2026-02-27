# Google Tag Manager - Sandboxed JavaScript API Reference

> Complete reference for all APIs available in GTM Custom Template sandboxed JavaScript.
> Based on official Google documentation: https://developers.google.com/tag-platform/tag-manager/templates/api

---

## Table of Contents

1. [Sandbox Environment](#sandbox-environment)
2. [The `data` Object and Callbacks](#the-data-object-and-callbacks)
3. [The `require()` Function](#the-require-function)
4. [Script Loading](#script-loading)
5. [Window Access](#window-access)
6. [Data Layer](#data-layer)
7. [Cookies](#cookies)
8. [URL and Referrer](#url-and-referrer)
9. [Encoding and Decoding](#encoding-and-decoding)
10. [Base64](#base64)
11. [JSON](#json)
12. [Hashing and Cryptography](#hashing-and-cryptography)
13. [Logging](#logging)
14. [Type Conversion](#type-conversion)
15. [Math](#math)
16. [Object Utilities](#object-utilities)
17. [Utility Functions](#utility-functions)
18. [Storage](#storage)
19. [Consent](#consent)
20. [Container and Event Metadata](#container-and-event-metadata)
21. [Pixel and Network Requests](#pixel-and-network-requests)
22. [IFrame Injection](#iframe-injection)
23. [Permission Querying](#permission-querying)
24. [Test APIs](#test-apis)
25. [Permissions Reference](#permissions-reference)
26. [Sandbox Constraints](#sandbox-constraints)

---

## Sandbox Environment

GTM custom templates run in a **sandboxed JavaScript environment** that is NOT standard browser JavaScript. The sandbox is based on ECMAScript 5.1 with select ES6 features (arrow functions, `const`/`let`). All browser APIs must be accessed through the sandboxed API layer using `require()`.

Key characteristics:
- No access to `window`, `document`, `location`, or any global browser objects
- No `new` keyword, no `this` keyword
- No global constructors (`String()`, `Number()`, `Date()`, `RegExp()`, etc.)
- No `setTimeout`, `setInterval`, `Promise`, or async/await
- No DOM manipulation
- Supported types: `null`, `undefined`, `string`, `number`, `boolean`, `array`, `object`, `function`
- Arrays and objects use literal syntax only (`[]`, `{}`)

---

## The `data` Object and Callbacks

The `data` object is a **global variable** automatically available in sandboxed JS (no `require()` needed). It contains all values configured through the template's UI parameters.

### Accessing Template Parameters

Each key on `data` corresponds to the `name` field in `___TEMPLATE_PARAMETERS___`:

```javascript
// If template has a TEXT parameter with name "dataLayerEventName"
const eventName = data.dataLayerEventName;

// If template has a CHECKBOX parameter with name "enableDeduplication"
const dedup = data.enableDeduplication; // boolean: true or false

// If template has a SELECT parameter with name "trackingMode"
const mode = data.trackingMode; // string value of selected option

// If template has a SIMPLE_TABLE parameter with name "customDimensions"
const table = data.customDimensions; // array of row objects
```

### `data.gtmOnSuccess()`

**What it does:** Signals to GTM that the tag executed successfully. Must be called when the tag completes its work. GTM uses this to report tag status in debug mode.

```javascript
// Call when everything succeeded
data.gtmOnSuccess();
```

### `data.gtmOnFailure()`

**What it does:** Signals to GTM that the tag failed. Call this when an error occurs (script failed to load, network error, etc.). GTM marks the tag as failed in debug mode.

```javascript
// Call when something went wrong
data.gtmOnFailure();
```

### Usage Pattern

```javascript
const injectScript = require('injectScript');

injectScript(
  'https://example.com/script.js',
  data.gtmOnSuccess,   // called on successful load
  data.gtmOnFailure,   // called on failure
  'myCacheToken'
);
```

---

## The `require()` Function

`require()` is a **global function** (no import needed) that loads built-in sandboxed APIs by name.

### Signature

```
require(apiName: string) => function | object
```

### Parameters

| Parameter | Type   | Description                      |
|-----------|--------|----------------------------------|
| `apiName` | string | Name of the built-in API to load |

### What It Does

Imports a built-in sandboxed function or object. Returns the function/object, or `undefined` if the name is not recognized.

### Permission

None required.

### Example

```javascript
const injectScript = require('injectScript');
const logToConsole = require('logToConsole');
const setInWindow = require('setInWindow');
const getUrl = require('getUrl');
const JSON = require('JSON');
const makeNumber = require('makeNumber');
const makeString = require('makeString');
const makeInteger = require('makeInteger');
const getType = require('getType');
```

> **WARNING — `require()` is needed for ALL APIs, including utility APIs.**
> There are NO global built-ins in GTM sandboxed JS. Even utility and type-conversion APIs that do not need a `___WEB_PERMISSIONS___` entry must still be loaded via `require()`. Skipping it causes an "Attempting to use undeclared variable" error at runtime.
>
> Common APIs that trip people up (no permission needed, but `require()` is mandatory):
> `makeNumber`, `makeString`, `makeInteger`, `getType`, `makeTableMap`, `JSON`, `Math`, `generateRandom`, `getTimestampMillis`

---

## Script Loading

### `injectScript`

```
injectScript(url: string, onSuccess: function, onFailure: function [, cacheToken: string]) => void
```

| Parameter    | Type     | Required | Description                                                    |
|-------------|----------|----------|----------------------------------------------------------------|
| `url`       | string   | Yes      | URL of the script to inject                                    |
| `onSuccess` | function | Yes      | Callback invoked when the script loads successfully             |
| `onFailure` | function | Yes      | Callback invoked when the script fails to load                  |
| `cacheToken`| string   | No       | Cache identifier; scripts with the same token are loaded once   |

**What it does:** Adds an asynchronous `<script>` tag to the page. When a `cacheToken` is provided, the script is loaded only once per page -- subsequent calls with the same token skip loading and call `onSuccess` immediately.

**Permission:** `inject_script` -- must list the allowed URL pattern(s).

```javascript
const injectScript = require('injectScript');
const scriptUrl = 'https://cdn.jsdelivr.net/gh/example/lib@1/tracker.js';

injectScript(scriptUrl, data.gtmOnSuccess, data.gtmOnFailure, 'myTracker');
```

---

## Window Access

### `setInWindow`

```
setInWindow(key: string, value: *, overrideExisting: boolean) => boolean
```

| Parameter          | Type    | Description                                               |
|-------------------|---------|-----------------------------------------------------------|
| `key`             | string  | Dot-separated path on the window object                   |
| `value`           | *       | Value to set                                              |
| `overrideExisting`| boolean | If `true`, sets the value even if the key already exists  |

**What it does:** Sets a value on the `window` object at the specified path. Returns `true` if the value was set successfully, `false` if the key already existed and `overrideExisting` was `false`.

**Permission:** `access_globals` -- the key must have `write` enabled. When `overrideExisting` is `true`, the key must have **both `read` and `write`** enabled (GTM classifies this as a readwrite operation).

```javascript
const setInWindow = require('setInWindow');
setInWindow('_myConfig', { debug: true, version: '1.0' }, true);
```

### `copyFromWindow`

```
copyFromWindow(key: string) => *
```

| Parameter | Type   | Description                             |
|-----------|--------|-----------------------------------------|
| `key`     | string | Dot-separated path on the window object |

**What it does:** Copies a value from the `window` object. Returns the value coerced to a sandboxed-JS-supported type, or `undefined` if the type is not supported (e.g., DOM elements).

**Permission:** `access_globals` -- the key must have `read` enabled.

```javascript
const copyFromWindow = require('copyFromWindow');
const existingConfig = copyFromWindow('_myConfig');
```

### `callInWindow`

```
callInWindow(pathToFunction: string, ...args) => *
```

| Parameter         | Type   | Description                                  |
|------------------|--------|----------------------------------------------|
| `pathToFunction` | string | Dot-separated path to a function on `window` |
| `...args`        | *      | Arguments to pass to the function            |

**What it does:** Calls a function that exists on the `window` object with the provided arguments. Returns the function's return value, or `undefined` if the return type is not supported in sandboxed JS. If the path does not point to a function, returns `undefined`.

**Permission:** `access_globals` -- the path must have `execute` enabled.

```javascript
const callInWindow = require('callInWindow');
callInWindow('myLibrary.init', 'UA-12345', { debug: false });
```

### `aliasInWindow`

```
aliasInWindow(toPath: string, fromPath: string) => boolean
```

| Parameter  | Type   | Description                                    |
|-----------|--------|------------------------------------------------|
| `toPath`  | string | Dot-separated destination path on `window`     |
| `fromPath`| string | Dot-separated source path on `window`          |

**What it does:** Creates an alias on the `window` object, effectively doing `window[toPath] = window[fromPath]`. Returns `true` if the operation succeeded.

**Permission:** `access_globals` -- `toPath` needs `write`, `fromPath` needs `read`.

```javascript
const aliasInWindow = require('aliasInWindow');
aliasInWindow('gtag', 'dataLayer.push');
```

### `createQueue`

```
createQueue(arrayKey: string) => function
```

| Parameter  | Type   | Description                                   |
|-----------|--------|-----------------------------------------------|
| `arrayKey`| string | Dot-separated window path for the backing array |

**What it does:** Creates an array at the specified window path (if it doesn't already exist) and returns a function that pushes items onto it. The returned function accepts any number of arguments, each pushed as a separate item.

**Permission:** `access_globals` -- the key needs both `read` and `write`.

```javascript
const createQueue = require('createQueue');
const dataLayerPush = createQueue('dataLayer');
dataLayerPush({'currency': 'USD'}, {'event': 'myConversion'});
```

### `createArgumentsQueue`

```
createArgumentsQueue(fnKey: string, arrayKey: string) => function
```

| Parameter  | Type   | Description                                         |
|-----------|--------|-----------------------------------------------------|
| `fnKey`   | string | Window path for the function that will be created    |
| `arrayKey`| string | Window path for the array that stores arguments      |

**What it does:** Creates a function on `window[fnKey]` that, when called, pushes its `arguments` object onto the array at `window[arrayKey]`. Returns the created function. This is the pattern used by `gtag()`.

**Permission:** `access_globals` -- both keys need `read` and `write`.

```javascript
const createArgumentsQueue = require('createArgumentsQueue');
const gtag = createArgumentsQueue('gtag', 'dataLayer');
gtag('set', {'currency': 'USD'});
gtag('event', 'purchase', {'value': 25.00});
```

---

## Data Layer

### `copyFromDataLayer`

```
copyFromDataLayer(key: string [, dataLayerVersion: number]) => *
```

| Parameter          | Type   | Required | Description                                         |
|-------------------|--------|----------|-----------------------------------------------------|
| `key`             | string | Yes      | Key in dot notation (e.g., `"a.b.c"`)              |
| `dataLayerVersion`| number | No       | Data layer version; defaults to `2` (merged model)  |

**What it does:** Returns the value for the given key from the data layer. With version 2 (default), the data layer model is used (merged state). With version 1, the most recently pushed object containing the key is searched.

**Permission:** `read_data_layer` -- the key must be listed.

```javascript
const copyFromDataLayer = require('copyFromDataLayer');
const currency = copyFromDataLayer('ecommerce.currency');
const eventName = copyFromDataLayer('event');
```

### `gtagSet`

```
gtagSet(settingsObject: object) => void
```

| Parameter        | Type   | Description                                |
|-----------------|--------|--------------------------------------------|
| `settingsObject`| object | Key-value pairs to set in global gtag state |

**What it does:** Pushes a gtag `set` command to the data layer. The settings are processed as soon as possible after the current event and always before queued data layer items. Designed for use in Initialization triggers.

**Permission:** `write_data_layer` -- all flattened keys in the object must be permitted.

```javascript
const gtagSet = require('gtagSet');
gtagSet({
  'ads_data_redaction': true,
  'url_passthrough': true
});
```

---

## Cookies

### `getCookieValues`

```
getCookieValues(name: string [, decode: boolean]) => Array<string>
```

| Parameter | Type    | Required | Description                                          |
|-----------|---------|----------|------------------------------------------------------|
| `name`    | string  | Yes      | Name of the cookie to read                           |
| `decode`  | boolean | No       | Whether to `decodeURIComponent` the values; default `true` |

**What it does:** Returns an array of values for all cookies with the given name. If no cookies match, returns an empty array.

**Permission:** `get_cookies` -- the cookie name must be listed.

```javascript
const getCookieValues = require('getCookieValues');
const gaValues = getCookieValues('_ga');
if (gaValues.length > 0) {
  const clientId = gaValues[0];
}
```

### `setCookie`

```
setCookie(name: string, value: string [, options: object, encode: boolean]) => void
```

| Parameter | Type    | Required | Description                                           |
|-----------|---------|----------|-------------------------------------------------------|
| `name`    | string  | Yes      | Cookie name                                           |
| `value`   | string  | Yes      | Cookie value                                          |
| `options` | object  | No       | Cookie attributes (see below)                         |
| `encode`  | boolean | No       | Whether to `encodeURIComponent` the value; default `true` |

**Options object properties:**

| Property   | Type    | Description                                                              |
|-----------|---------|--------------------------------------------------------------------------|
| `domain`  | string  | Domain scope; set to `'auto'` to write on broadest possible domain       |
| `path`    | string  | Path scope (e.g., `'/'`)                                                 |
| `max-age` | number  | Maximum age in seconds                                                   |
| `expires` | string  | Expiration as UTC date string (e.g., `'Thu, 01 Dec 2025 12:00:00 UTC'`) |
| `secure`  | boolean | Whether the cookie requires HTTPS                                        |
| `samesite`| string  | `'Strict'`, `'Lax'`, or `'None'`                                        |

**What it does:** Sets (or deletes) a cookie with the given name, value, and attributes.

**Permission:** `set_cookies` -- the cookie name must be listed, with optional domain/path/secure/expiration constraints.

```javascript
const setCookie = require('setCookie');
setCookie('tracking_consent', 'granted', {
  domain: 'auto',
  path: '/',
  'max-age': 60 * 60 * 24 * 365,  // 1 year
  secure: true,
  samesite: 'Lax'
});
```

---

## URL and Referrer

### `getUrl`

```
getUrl(component: string) => string
```

| Parameter   | Type   | Required | Description                                                         |
|------------|--------|----------|---------------------------------------------------------------------|
| `component`| string | No       | One of: `protocol`, `host`, `port`, `path`, `query`, `extension`, `fragment` |

**What it does:** Returns the current page URL, or a specific component of it. When `component` is omitted, returns the full `href`.

**Permission:** `get_url` -- must specify which components are allowed.

```javascript
const getUrl = require('getUrl');
const fullUrl = getUrl();           // 'https://example.com:8080/page?q=test#section'
const protocol = getUrl('protocol'); // 'https:'
const host = getUrl('host');         // 'example.com:8080'
const path = getUrl('path');         // '/page'
const query = getUrl('query');       // '?q=test'
const fragment = getUrl('fragment'); // '#section'
```

### `getQueryParameters`

```
getQueryParameters(queryKey: string [, retrieveAll: boolean]) => string | Array<string>
```

| Parameter    | Type    | Required | Description                                           |
|-------------|---------|----------|-------------------------------------------------------|
| `queryKey`  | string  | Yes      | Query parameter name to look up                       |
| `retrieveAll`| boolean| No       | If `true`, returns all values as an array; default `false` |

**What it does:** Returns the value(s) of the specified query parameter from the current page URL. Returns the first matching value by default, or all matching values as an array when `retrieveAll` is `true`.

**Permission:** `get_url` -- must allow the `query` component and specify the `queryKey`.

```javascript
const getQueryParameters = require('getQueryParameters');
const utmSource = getQueryParameters('utm_source');
const allTags = getQueryParameters('tag', true); // returns array
```

### `getReferrerUrl`

```
getReferrerUrl(component: string) => string
```

| Parameter   | Type   | Required | Description                                                    |
|------------|--------|----------|----------------------------------------------------------------|
| `component`| string | No       | One of: `protocol`, `host`, `port`, `path`, `query`, `extension` |

**What it does:** Returns the referrer URL or a specified component of it. Note: `fragment` is NOT available for referrer URLs.

**Permission:** `get_referrer` -- must specify which components are allowed.

```javascript
const getReferrerUrl = require('getReferrerUrl');
const referrer = getReferrerUrl();
const referrerHost = getReferrerUrl('host');
```

### `getReferrerQueryParameters`

```
getReferrerQueryParameters(queryKey: string [, retrieveAll: boolean]) => string | Array<string>
```

| Parameter    | Type    | Required | Description                                            |
|-------------|---------|----------|--------------------------------------------------------|
| `queryKey`  | string  | Yes      | Query parameter name to look up in the referrer URL    |
| `retrieveAll`| boolean| No       | If `true`, returns all values as an array; default `false` |

**What it does:** Same as `getQueryParameters` but reads from the referrer URL instead of the current page URL.

**Permission:** `get_referrer` -- must allow the `query` component and specify the `queryKey`.

```javascript
const getReferrerQueryParameters = require('getReferrerQueryParameters');
const referrerSource = getReferrerQueryParameters('utm_source');
```

### `parseUrl`

```
parseUrl(url: string) => object | undefined
```

| Parameter | Type   | Description       |
|-----------|--------|-------------------|
| `url`     | string | Full URL to parse |

**What it does:** Parses a URL string into its component parts. Returns `undefined` for malformed URLs. Does NOT require any permissions (operates on the provided string, not on the browser).

**Permission:** None.

**Returns an object with:**

| Property       | Type   | Example                              |
|---------------|--------|--------------------------------------|
| `href`        | string | `'https://user:pass@example.com:8080/path?q=1#hash'` |
| `origin`      | string | `'https://example.com:8080'`         |
| `protocol`    | string | `'https:'`                           |
| `username`    | string | `'user'`                             |
| `password`    | string | `'pass'`                             |
| `host`        | string | `'example.com:8080'`                 |
| `hostname`    | string | `'example.com'`                      |
| `port`        | string | `'8080'`                             |
| `pathname`    | string | `'/path'`                            |
| `search`      | string | `'?q=1'`                             |
| `searchParams`| object | `{ q: '1' }`                         |
| `hash`        | string | `'#hash'`                            |

```javascript
const parseUrl = require('parseUrl');
const parsed = parseUrl('https://example.com/page?id=123&type=event');
if (parsed) {
  const host = parsed.hostname;        // 'example.com'
  const id = parsed.searchParams.id;   // '123'
}
```

---

## Encoding and Decoding

### `encodeUri`

```
encodeUri(uri: string) => string | undefined
```

| Parameter | Type   | Description    |
|-----------|--------|----------------|
| `uri`     | string | Complete URI   |

**What it does:** Encodes a complete URI by escaping special characters, preserving URI-structural characters (`:`, `/`, `?`, `#`, etc.). Equivalent to native `encodeURI()`. Returns `undefined` if the input contains a lone surrogate.

**Permission:** None.

```javascript
const encodeUri = require('encodeUri');
const encoded = encodeUri('https://example.com/path with spaces?q=hello world');
// 'https://example.com/path%20with%20spaces?q=hello%20world'
```

### `encodeUriComponent`

```
encodeUriComponent(str: string) => string | undefined
```

| Parameter | Type   | Description       |
|-----------|--------|-------------------|
| `str`     | string | URI component     |

**What it does:** Encodes a URI component by escaping all special characters including URI-structural ones. Equivalent to native `encodeURIComponent()`. Returns `undefined` if the input contains a lone surrogate.

**Permission:** None.

```javascript
const encodeUriComponent = require('encodeUriComponent');
const encoded = encodeUriComponent('hello world&foo=bar');
// 'hello%20world%26foo%3Dbar'
```

### `decodeUri`

```
decodeUri(encoded_uri: string) => string | undefined
```

| Parameter     | Type   | Description               |
|--------------|--------|---------------------------|
| `encoded_uri`| string | Previously encoded URI    |

**What it does:** Decodes an encoded URI. Equivalent to native `decodeURI()`. Returns `undefined` if the input is invalid.

**Permission:** None.

```javascript
const decodeUri = require('decodeUri');
const decoded = decodeUri('https://example.com/path%20with%20spaces');
// 'https://example.com/path with spaces'
```

### `decodeUriComponent`

```
decodeUriComponent(encoded_uri_component: string) => string | undefined
```

| Parameter                | Type   | Description                      |
|-------------------------|--------|----------------------------------|
| `encoded_uri_component` | string | Previously encoded URI component |

**What it does:** Decodes an encoded URI component. Equivalent to native `decodeURIComponent()`. Returns `undefined` if the input is invalid.

**Permission:** None.

```javascript
const decodeUriComponent = require('decodeUriComponent');
const decoded = decodeUriComponent('hello%20world%26foo%3Dbar');
// 'hello world&foo=bar'
```

---

## Base64

### `toBase64`

```
toBase64(input: string) => string | undefined
```

| Parameter | Type   | Description       |
|-----------|--------|-------------------|
| `input`   | string | String to encode  |

**What it does:** Encodes a string into its base64 representation. Supports unicode characters. Returns `undefined` if the input is invalid.

**Permission:** None.

```javascript
const toBase64 = require('toBase64');
const encoded = toBase64('Hello, World!');
// 'SGVsbG8sIFdvcmxkIQ=='
```

### `fromBase64`

```
fromBase64(base64EncodedString: string) => string | undefined
```

| Parameter             | Type   | Description           |
|----------------------|--------|-----------------------|
| `base64EncodedString`| string | Base64 encoded string |

**What it does:** Decodes a base64-encoded string back to its original representation. Returns `undefined` if the input is not valid base64.

**Permission:** None.

```javascript
const fromBase64 = require('fromBase64');
const decoded = fromBase64('SGVsbG8sIFdvcmxkIQ==');
// 'Hello, World!'
```

---

## JSON

### `JSON.parse`

```
JSON.parse(stringInput: string) => *
```

| Parameter     | Type   | Description                                   |
|--------------|--------|-----------------------------------------------|
| `stringInput`| string | JSON string to parse (coerced to string if not)|

**What it does:** Parses a JSON string into an object, array, or primitive. Returns `undefined` if the input is malformed JSON.

**Permission:** None.

### `JSON.stringify`

```
JSON.stringify(value: *) => string | undefined
```

| Parameter | Type | Description          |
|-----------|------|----------------------|
| `value`   | *    | Value to serialize   |

**What it does:** Converts a value to its JSON string representation. Returns `undefined` if the value contains circular references.

**Permission:** None.

```javascript
const JSON = require('JSON');

const obj = JSON.parse('{"key": "value", "count": 42}');
const str = JSON.stringify({ event: 'purchase', value: 25.00 });
```

---

## Hashing and Cryptography

### `sha256`

```
sha256(input: string, onSuccess: function [, onFailure: function, options: object]) => void
```

| Parameter   | Type     | Required | Description                                              |
|------------|----------|----------|----------------------------------------------------------|
| `input`    | string   | Yes      | String to hash                                           |
| `onSuccess`| function | Yes      | Callback receiving the digest string                     |
| `onFailure`| function | No       | Callback invoked on error                                |
| `options`  | object   | No       | Configuration; supports `outputEncoding`: `'base64'` (default) or `'hex'` |

**What it does:** Calculates the SHA-256 digest of the input string and passes the result to the `onSuccess` callback. This is an **asynchronous** operation.

**Permission:** None.

```javascript
const sha256 = require('sha256');
const sendPixel = require('sendPixel');

// Default base64 encoding
sha256('user@example.com', (digest) => {
  sendPixel('https://example.com/collect?hash=' + digest);
  data.gtmOnSuccess();
}, data.gtmOnFailure);

// Hex encoding
sha256('user@example.com', (digest) => {
  sendPixel('https://example.com/collect?hash=' + digest);
  data.gtmOnSuccess();
}, data.gtmOnFailure, {outputEncoding: 'hex'});
```

---

## Logging

### `logToConsole`

```
logToConsole(obj1 [, obj2, ... objN]) => void
```

| Parameter     | Type | Description               |
|--------------|------|---------------------------|
| `obj1...objN`| *    | Values to log to console  |

**What it does:** Logs the provided arguments to the browser's developer console. By default, only works in GTM debug/preview mode. Can be configured to log in production as well.

**Permission:** `logging` -- configure `environments` as `"debug"` (default, recommended) or `"all"`.

```javascript
const logToConsole = require('logToConsole');
logToConsole('My Tag: initialized with config', data);
logToConsole('Debug value:', someVariable, 'another value:', anotherVariable);
```

---

## Type Conversion

### `makeString`

```
makeString(value: *) => string
```

**What it does:** Converts any value to its string representation. Equivalent to `String(value)` in standard JS.

**Permission:** None.

```javascript
const makeString = require('makeString');
const str = makeString(123);        // '123'
const str2 = makeString(true);      // 'true'
const str3 = makeString(undefined); // 'undefined'
```

### `makeNumber`

```
makeNumber(value: *) => number
```

**What it does:** Converts any value to a number (including floating point). Equivalent to `Number(value)` in standard JS.

**Permission:** None.

```javascript
const makeNumber = require('makeNumber');
const num = makeNumber('42.5');   // 42.5
const nan = makeNumber('hello');  // NaN
```

### `makeInteger`

```
makeInteger(value: *) => number
```

**What it does:** Converts any value to an integer number (truncates decimals). Equivalent to `parseInt(value, 10)` in standard JS.

**Permission:** None.

```javascript
const makeInteger = require('makeInteger');
const int = makeInteger('42.9');  // 42
const int2 = makeInteger(3.14);  // 3
```

### `makeTableMap`

```
makeTableMap(tableObj: Array, keyColumnName: string, valueColumnName: string) => object | null
```

| Parameter        | Type   | Description                         |
|-----------------|--------|-------------------------------------|
| `tableObj`      | Array  | Table parameter value (array of row objects) |
| `keyColumnName` | string | Column name to use as map keys      |
| `valueColumnName`| string| Column name to use as map values    |

**What it does:** Converts a `SIMPLE_TABLE` template parameter (which is an array of row objects) into a key-value map. Returns `null` if the table is empty.

**Permission:** None.

```javascript
const makeTableMap = require('makeTableMap');

// data.customParams is a SIMPLE_TABLE with columns "paramName" and "paramValue"
// e.g., [{paramName: 'color', paramValue: 'blue'}, {paramName: 'size', paramValue: 'large'}]
const params = makeTableMap(data.customParams, 'paramName', 'paramValue');
// Result: {color: 'blue', size: 'large'}
```

### `getType`

```
getType(value: *) => string
```

| Parameter | Type | Description        |
|-----------|------|--------------------|
| `value`   | *    | Value to type-check|

**What it does:** Returns a string indicating the type of the value. Unlike the standard `typeof` operator, this correctly differentiates `'array'` from `'object'` and identifies `'null'` as its own type.

**Permission:** None.

**Possible return values:** `'undefined'`, `'null'`, `'boolean'`, `'number'`, `'string'`, `'object'`, `'array'`, `'function'`

```javascript
const getType = require('getType');
getType(undefined);       // 'undefined'
getType(null);            // 'null'
getType(true);            // 'boolean'
getType(42);              // 'number'
getType('hello');         // 'string'
getType({a: 1});          // 'object'
getType([1, 2, 3]);       // 'array'
getType(function() {});   // 'function'
```

---

## Math

### `Math`

The `Math` object provides standard mathematical functions. Parameter values are automatically converted to numbers.

**Permission:** None.

| Method         | Description                              |
|---------------|------------------------------------------|
| `Math.abs(x)` | Absolute value                           |
| `Math.floor(x)`| Round down to nearest integer           |
| `Math.ceil(x)` | Round up to nearest integer             |
| `Math.round(x)`| Round to nearest integer                |
| `Math.max(a, b, ...)`| Largest of the given values       |
| `Math.min(a, b, ...)`| Smallest of the given values      |
| `Math.pow(base, exp)` | Base raised to exponent power    |
| `Math.sqrt(x)`| Square root                              |

```javascript
const Math = require('Math');
const abs = Math.abs(-5);         // 5
const rounded = Math.round(3.7);  // 4
const floored = Math.floor(3.7);  // 3
const ceiled = Math.ceil(3.2);    // 4
const max = Math.max(1, 5, 3);    // 5
const power = Math.pow(2, 10);    // 1024
const root = Math.sqrt(16);       // 4
```

---

## Object Utilities

### `Object`

The `Object` utility provides standard object manipulation methods.

**Permission:** None.

| Method                             | Description                                      |
|-----------------------------------|--------------------------------------------------|
| `Object.keys(obj)`               | Returns array of own enumerable property names    |
| `Object.values(obj)`             | Returns array of own enumerable property values   |
| `Object.entries(obj)`            | Returns array of `[key, value]` pairs             |
| `Object.freeze(obj)`             | Freezes object, preventing modifications          |
| `Object.delete(obj, keyToDelete)`| Deletes a top-level property from the object      |

**Note:** `Object.delete()` can only remove top-level keys. It cannot handle nested key paths or array elements.

```javascript
const Object = require('Object');

const config = { name: 'tracker', version: 2, debug: true };
const keys = Object.keys(config);       // ['name', 'version', 'debug']
const values = Object.values(config);   // ['tracker', 2, true]
const entries = Object.entries(config);  // [['name','tracker'], ['version',2], ['debug',true]]

Object.freeze(config);     // config can no longer be modified
Object.delete(config, 'debug');  // removes 'debug' key (before freeze)
```

---

## Utility Functions

### `generateRandom`

```
generateRandom(min: number, max: number) => number
```

| Parameter | Type   | Description     |
|-----------|--------|-----------------|
| `min`     | number | Minimum value   |
| `max`     | number | Maximum value   |

**What it does:** Returns a random integer between `min` and `max` (inclusive).

**Permission:** None.

```javascript
const generateRandom = require('generateRandom');
const cacheBuster = generateRandom(0, 2147483647);
```

### `getTimestampMillis`

```
getTimestampMillis() => number
```

**What it does:** Returns the current time in milliseconds since the Unix epoch (January 1, 1970). Equivalent to `Date.now()`.

**Permission:** None.

```javascript
const getTimestampMillis = require('getTimestampMillis');
const now = getTimestampMillis(); // e.g., 1708617600000
```

### `getTimestamp` (DEPRECATED)

```
getTimestamp() => number
```

**What it does:** Same as `getTimestampMillis`. **Deprecated** -- use `getTimestampMillis` instead.

**Permission:** None.

### `callLater`

```
callLater(fn: function) => void
```

| Parameter | Type     | Description                       |
|-----------|----------|-----------------------------------|
| `fn`      | function | Function to call asynchronously   |

**What it does:** Schedules a function to be called asynchronously, equivalent to `setTimeout(fn, 0)`. This is the only way to schedule deferred work in sandboxed JS (since `setTimeout` is not available).

**Permission:** None.

```javascript
const callLater = require('callLater');
callLater(function() {
  // This runs asynchronously after the current execution completes
  const value = copyFromWindow('_result');
  data.gtmOnSuccess();
});
```

---

## Storage

### `localStorage`

```
localStorage.getItem(key: string) => string | null
localStorage.setItem(key: string, value: string) => boolean
localStorage.removeItem(key: string) => void
```

| Method        | Parameters                     | Returns              | Description                        |
|--------------|--------------------------------|----------------------|------------------------------------|
| `getItem`    | `key` (string)                 | string or `null`     | Reads a value from localStorage    |
| `setItem`    | `key` (string), `value` (string)| boolean             | Writes a value; returns `true` if successful |
| `removeItem` | `key` (string)                 | void                 | Removes a key from localStorage    |

**What it does:** Provides access to the browser's `localStorage` API for persistent key-value storage that survives page reloads and browser restarts.

**Permission:** `access_local_storage` -- each key must be listed with read/write access.

```javascript
const localStorage = require('localStorage');
const stored = localStorage.getItem('myTemplate_clientId');
if (!stored) {
  localStorage.setItem('myTemplate_clientId', 'abc123');
}
```

### `templateStorage`

```
templateStorage.getItem(key: string) => *
templateStorage.setItem(key: string, value: *) => void
templateStorage.removeItem(key: string) => void
templateStorage.clear() => void
```

| Method        | Parameters                     | Returns | Description                                          |
|--------------|--------------------------------|---------|------------------------------------------------------|
| `getItem`    | `key` (string)                 | *       | Reads a value from template storage                  |
| `setItem`    | `key` (string), `value` (any)  | void    | Writes any value (not just strings)                  |
| `removeItem` | `key` (string)                 | void    | Removes a key                                        |
| `clear`      | none                           | void    | Removes all keys for this template                   |

**What it does:** Provides per-template key-value storage that persists for the lifetime of the page (cleared on navigation). Unlike `localStorage`, it can store any type (not just strings) and is scoped to the specific template.

**Important:** Unlike the Web Storage API, `templateStorage` does NOT serialize/deserialize values. It stores and returns references, not copies.

**Permission:** `access_template_storage`.

```javascript
const templateStorage = require('templateStorage');
templateStorage.setItem('initialized', true);
templateStorage.setItem('config', { debug: true, version: 2 });

const isInit = templateStorage.getItem('initialized'); // true (boolean, not string)
const config = templateStorage.getItem('config');       // {debug: true, version: 2}
```

---

## Consent

### `isConsentGranted`

```
isConsentGranted(consentType: string) => boolean
```

| Parameter     | Type   | Description                |
|--------------|--------|----------------------------|
| `consentType`| string | Consent type to check      |

**What it does:** Returns `true` if the specified consent type is granted. If the consent type has not been set at all, it is treated as granted (returns `true`). Tags with "always fire" consent enabled will always see consent as granted.

**Permission:** `access_consent` -- read access required for the consent type.

**Common consent types:** `'ad_storage'`, `'analytics_storage'`, `'functionality_storage'`, `'personalization_storage'`, `'security_storage'`

```javascript
const isConsentGranted = require('isConsentGranted');
if (isConsentGranted('ad_storage')) {
  sendFullPixel();
} else {
  sendPixelWithoutCookies();
}
```

### `addConsentListener`

```
addConsentListener(consentType: string, listener: function) => void
```

| Parameter     | Type     | Description                                          |
|--------------|----------|------------------------------------------------------|
| `consentType`| string   | Consent type to monitor                              |
| `listener`   | function | Callback: `(consentType: string, granted: boolean)`  |

**What it does:** Registers a callback that fires whenever the specified consent type transitions between granted and denied states. The listener receives the consent type and a boolean indicating whether it is now granted.

**Permission:** `access_consent` -- read access required for the consent type.

```javascript
const isConsentGranted = require('isConsentGranted');
const addConsentListener = require('addConsentListener');

if (!isConsentGranted('ad_storage')) {
  let wasCalled = false;
  addConsentListener('ad_storage', (consentType, granted) => {
    if (wasCalled) return;
    if (granted) {
      wasCalled = true;
      sendFullPixel();
    }
  });
}
```

### `setDefaultConsentState`

```
setDefaultConsentState(consentSettings: object) => void
```

| Parameter        | Type   | Description                                    |
|-----------------|--------|------------------------------------------------|
| `consentSettings`| object | Consent types mapped to `'granted'` or `'denied'`, plus optional `region` and `wait_for_update` |

**What it does:** Pushes a default consent state to the data layer. Processed as soon as possible after the current event, always before Initialization triggers. Use this in Consent Initialization triggers.

**Permission:** `access_consent` -- write access required for all consent types in the object.

```javascript
const setDefaultConsentState = require('setDefaultConsentState');
setDefaultConsentState({
  'ad_storage': 'denied',
  'analytics_storage': 'granted',
  'wait_for_update': 500,
  'region': ['ES', 'US-AK']
});
```

### `updateConsentState`

```
updateConsentState(consentSettings: object) => void
```

| Parameter        | Type   | Description                                     |
|-----------------|--------|-------------------------------------------------|
| `consentSettings`| object | Consent types mapped to `'granted'` or `'denied'` |

**What it does:** Pushes a consent state update to the data layer, processed as soon as possible after the current event and always before any queued data layer items.

**Permission:** `access_consent` -- write access required for all consent types in the object.

```javascript
const updateConsentState = require('updateConsentState');
updateConsentState({
  'ad_storage': 'granted',
  'analytics_storage': 'granted'
});
```

---

## Container and Event Metadata

### `getContainerVersion`

```
getContainerVersion() => object
```

**What it does:** Returns an object containing metadata about the current GTM container.

**Permission:** `read_container_data`.

**Returns an object with:**

| Property           | Type    | Description                                         |
|-------------------|---------|-----------------------------------------------------|
| `containerId`     | string  | GTM container ID (e.g., `'GTM-XXXX'`)              |
| `version`         | string  | Container version number                             |
| `debugMode`       | boolean | Whether GTM is in debug/preview mode                |
| `previewMode`     | boolean | Whether GTM is in preview mode                      |
| `environmentName` | string  | Name of the current environment (e.g., `'Live'`)   |
| `environmentMode` | string  | Environment mode                                    |

```javascript
const getContainerVersion = require('getContainerVersion');
const cv = getContainerVersion();

if (cv.debugMode) {
  logToConsole('Running in debug mode, container version:', cv.version);
}

const pixelUrl = 'https://pixel.com/' +
  '?version=' + cv.version +
  '&ctid=' + cv.containerId +
  '&debugMode=' + cv.debugMode;
```

### `addEventCallback`

```
addEventCallback(callback: function) => void
```

| Parameter  | Type     | Description                                                   |
|-----------|----------|---------------------------------------------------------------|
| `callback`| function | Callback: `(containerId: string, eventData: object) => void` |

**What it does:** Registers a callback that fires after all tags for the current event have either executed or timed out. The `eventData` object contains a `tags` array with execution status and timing information for each tag.

**Permission:** `read_event_metadata`.

```javascript
const addEventCallback = require('addEventCallback');
const logToConsole = require('logToConsole');

addEventCallback(function(ctid, eventData) {
  logToConsole('Tag count for ' + ctid + ': ' + eventData['tags'].length);
});
```

### `readTitle`

```
readTitle() => string
```

**What it does:** Returns the value of `document.title`.

**Permission:** `read_title`.

```javascript
const readTitle = require('readTitle');
const pageTitle = readTitle(); // e.g., 'My Website - Home'
```

### `readCharacterSet`

```
readCharacterSet() => string
```

**What it does:** Returns the value of `document.characterSet`.

**Permission:** `read_character_set`.

```javascript
const readCharacterSet = require('readCharacterSet');
const charset = readCharacterSet(); // e.g., 'UTF-8'
```

### `readAnalyticsStorage`

```
readAnalyticsStorage(cookieOptions: object) => object
```

| Parameter       | Type   | Required | Description                                            |
|----------------|--------|----------|--------------------------------------------------------|
| `cookieOptions`| object | No       | Optional: `cookie_prefix`, `cookie_domain`, `cookie_path` |

**What it does:** Retrieves stored analytics data including the Google Analytics client ID and session information.

**Permission:** `read_analytics_storage`.

**Returns an object with:**

| Property    | Type   | Description                                            |
|------------|--------|--------------------------------------------------------|
| `client_id`| string | The GA client ID                                       |
| `sessions` | array  | Array of session objects with `measurement_id`, `session_id`, `session_number` |

```javascript
const readAnalyticsStorage = require('readAnalyticsStorage');
const analyticsData = readAnalyticsStorage();
const clientId = analyticsData.client_id;
```

---

## Pixel and Network Requests

### `sendPixel`

```
sendPixel(url: string [, onSuccess: function, onFailure: function]) => void
```

| Parameter   | Type     | Required | Description                          |
|------------|----------|----------|--------------------------------------|
| `url`      | string   | Yes      | URL to send the GET request to       |
| `onSuccess`| function | No       | Called when the request succeeds      |
| `onFailure`| function | No       | Called when the request fails         |

**What it does:** Sends a GET request to the specified URL by loading it as an image. Success or failure depends on whether a valid response is received. This is a fire-and-forget mechanism commonly used for tracking pixels and measurement endpoints.

**Permission:** `send_pixel` -- must list the allowed URL pattern(s).

```javascript
const sendPixel = require('sendPixel');
const encodeUri = require('encodeUri');

const url = 'https://collect.example.com/pixel?event=pageview&url=' +
  encodeUri(getUrl());
sendPixel(url, data.gtmOnSuccess, data.gtmOnFailure);
```

---

## IFrame Injection

### `injectHiddenIframe`

```
injectHiddenIframe(url: string, onSuccess: function) => void
```

| Parameter   | Type     | Description                     |
|------------|----------|---------------------------------|
| `url`      | string   | URL to set as the iframe `src`  |
| `onSuccess`| function | Called when the iframe loads     |

**What it does:** Adds an invisible `<iframe>` element to the page. Used for legacy tracking methods that require iframe-based pixel loading.

**Permission:** `inject_hidden_iframe` -- must list the allowed URL pattern(s).

```javascript
const injectHiddenIframe = require('injectHiddenIframe');
injectHiddenIframe(
  'https://example.com/conversion?id=12345',
  data.gtmOnSuccess
);
```

---

## Permission Querying

### `queryPermission`

```
queryPermission(permission: string, ...args) => boolean
```

| Parameter    | Type   | Description                                   |
|-------------|--------|-----------------------------------------------|
| `permission`| string | Permission name to check                      |
| `...args`   | *      | Permission-specific arguments (see examples)  |

**What it does:** Checks whether a specific permission is granted for the template at runtime. Use this to gracefully degrade functionality when certain permissions are not available.

**Permission:** None (this API queries permissions, it does not require one).

**Permission-specific argument patterns:**

| Permission Check                     | Call Pattern                                      |
|-------------------------------------|---------------------------------------------------|
| Read URL component                  | `queryPermission('get_url', 'query', 'utm_source')` |
| Read cookie                         | `queryPermission('get_cookies', '_ga')`             |
| Set cookie                          | `queryPermission('set_cookies', '_ga')`             |
| Send pixel to URL                   | `queryPermission('send_pixel', url)`                |
| Inject script from URL              | `queryPermission('inject_script', url)`             |
| Read container data                 | `queryPermission('read_container_data')`            |
| Read global variable                | `queryPermission('access_globals', 'myVar', 'read')`|
| Write global variable               | `queryPermission('access_globals', 'myVar', 'write')`|
| Execute global function             | `queryPermission('access_globals', 'myFn', 'execute')`|

```javascript
const queryPermission = require('queryPermission');
const getUrl = require('getUrl');
const getCookieValues = require('getCookieValues');

if (queryPermission('get_url', 'query', 'utm_source')) {
  const source = getQueryParameters('utm_source');
}

if (queryPermission('get_cookies', '_ga')) {
  const gaCookie = getCookieValues('_ga');
}
```

---

## Test APIs

These APIs are available only in the `___TESTS___` section of a `.tpl` file. They are used for unit testing template logic.

### `runCode`

```
runCode(data: object) => * | undefined
```

| Parameter | Type   | Description                                     |
|-----------|--------|-------------------------------------------------|
| `data`    | object | Mock data object simulating template parameters  |

**What it does:** Executes the template's sandboxed JS code with the provided mock data. For variable templates, returns the variable's value. For tag templates, returns `undefined`.

```javascript
const mockData = {
  dataLayerEventName: 'calendly',
  enableDeduplication: true,
  trackEventTypeViewed: true
};
runCode(mockData);
```

### `mock`

```
mock(apiName: string, returnValue: * | function) => void
```

| Parameter    | Type        | Description                                                |
|-------------|-------------|------------------------------------------------------------|
| `apiName`   | string      | Name of the sandboxed API to mock (same as `require()` name)|
| `returnValue`| * or function | Value to return, or function to execute when API is called |

**What it does:** Replaces a sandboxed API with a mock implementation for testing. Mocks are automatically reset before each test scenario. When `returnValue` is a function, that function is called instead of the real API.

```javascript
mock('setInWindow', function(key, value, override) {
  return true;
});

mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
  onSuccess();  // simulate successful script load
});

// Mock a simple return value
mock('copyFromWindow', 'existingValue');
```

### `mockObject`

```
mockObject(apiName: string, objectMock: object) => void
```

| Parameter    | Type   | Description                                     |
|-------------|--------|-------------------------------------------------|
| `apiName`   | string | Name of the API that returns an object           |
| `objectMock`| object | Mock object to return instead of the real one    |

**What it does:** Replaces an object-returning API (like `localStorage`, `templateStorage`, `JSON`) with a mock object.

```javascript
mockObject('templateStorage', {
  getItem: function(key) { return 'mockValue'; },
  setItem: function(key, value) { },
  removeItem: function(key) { }
});
```

### `assertApi`

```
assertApi(apiName: string) => AssertionMatcher
```

| Parameter | Type   | Description                                      |
|-----------|--------|--------------------------------------------------|
| `apiName` | string | Name of the sandboxed API to assert against       |

**What it does:** Returns a fluent assertion matcher that checks whether an API was called and with what arguments.

**Available matchers:**

| Matcher                           | Description                                        |
|----------------------------------|----------------------------------------------------|
| `.wasCalled()`                   | Asserts the API was called at least once           |
| `.wasNotCalled()`                | Asserts the API was never called                   |
| `.wasCalledWith(...expected)`    | Asserts the API was called with exactly these args |
| `.wasNotCalledWith(...expected)` | Asserts the API was NOT called with these args     |

```javascript
runCode(mockData);
assertApi('setInWindow').wasCalled();
assertApi('injectScript').wasCalled();
assertApi('gtmOnSuccess').wasCalled();
assertApi('gtmOnFailure').wasNotCalled();
```

### `assertThat`

```
assertThat(actual: *, opt_message: string) => AssertionMatcher
```

| Parameter    | Type   | Required | Description                            |
|-------------|--------|----------|----------------------------------------|
| `actual`    | *      | Yes      | Value to assert against                |
| `opt_message`| string | No      | Custom message shown on failure        |

**What it does:** Returns a fluent assertion matcher for value-based assertions. Modeled after Google's Truth testing library.

**Available matchers:**

| Category      | Matchers                                                                                      |
|--------------|-----------------------------------------------------------------------------------------------|
| Null/Defined | `isUndefined()`, `isDefined()`, `isNull()`, `isNotNull()`                                    |
| Boolean      | `isFalse()`, `isTrue()`, `isFalsy()`, `isTruthy()`                                           |
| Numeric      | `isNaN()`, `isNotNaN()`, `isInfinity()`, `isNotInfinity()`                                   |
| Equality     | `isEqualTo(expected)`, `isNotEqualTo(expected)`, `isStrictlyEqualTo(expected)`, `isNotStrictlyEqualTo(expected)` |
| Comparison   | `isGreaterThan(val)`, `isGreaterThanOrEqualTo(val)`, `isLessThan(val)`, `isLessThanOrEqualTo(val)` |
| Membership   | `isAnyOf(...values)`, `isNoneOf(...values)`                                                   |
| Collection   | `contains(element)`, `doesNotContain(element)`, `containsExactly(...elements)`, `doesNotContainExactly(...elements)` |
| Length       | `hasLength(length)`, `isEmpty()`, `isNotEmpty()`                                              |
| Type checks  | `isArray()`, `isBoolean()`, `isFunction()`, `isNumber()`, `isObject()`, `isString()`          |

```javascript
assertThat(savedConfig.dataLayerEventName).isEqualTo('calendly');
assertThat(savedConfig.enableDeduplication).isTrue();
assertThat(savedConfig.allowedEvents.event_type_viewed).isTrue();
assertThat(savedConfig.allowedEvents.profile_page_viewed).isUndefined();
assertThat(result, 'Expected result to be a string').isString();
assertThat(items).hasLength(3);
assertThat(values).contains('expected_value');
```

### `fail`

```
fail(opt_message: string) => void
```

| Parameter    | Type   | Required | Description               |
|-------------|--------|----------|---------------------------|
| `opt_message`| string | No      | Message to display on failure |

**What it does:** Immediately fails the current test and prints the provided message.

```javascript
if (someCondition) {
  fail('This code path should not have been reached');
}
```

---

## Permissions Reference

This section documents the permission keys used in the `___WEB_PERMISSIONS___` section of a `.tpl` file.

| Permission Key           | APIs That Require It                                               | Configuration                                               |
|-------------------------|--------------------------------------------------------------------|-------------------------------------------------------------|
| `access_globals`        | `setInWindow`, `copyFromWindow`, `callInWindow`, `aliasInWindow`, `createQueue`, `createArgumentsQueue` | List of dot-separated keys with `read`/`write`/`execute` flags |
| `access_local_storage`  | `localStorage`                                                     | List of key names with `read`/`write` flags                  |
| `access_template_storage`| `templateStorage`                                                 | No configuration needed                                      |
| `access_consent`        | `isConsentGranted`, `addConsentListener`, `setDefaultConsentState`, `updateConsentState` | Consent types with `read`/`write` flags                      |
| `get_cookies`           | `getCookieValues`                                                  | List of allowed cookie names                                 |
| `set_cookies`           | `setCookie`                                                        | Table of cookie names with domain/path/secure/expiration constraints |
| `get_url`               | `getUrl`, `getQueryParameters`                                     | Allowed URL components (`protocol`, `host`, `port`, `path`, `query`, `extension`, `fragment`) and optional `queryKeys` |
| `get_referrer`          | `getReferrerUrl`, `getReferrerQueryParameters`                     | Allowed URL components (no `fragment`) and optional `queryKeys` |
| `inject_script`         | `injectScript`                                                     | List of allowed URL patterns                                 |
| `inject_hidden_iframe`  | `injectHiddenIframe`                                               | List of allowed URL patterns                                 |
| `send_pixel`            | `sendPixel`                                                        | List of allowed URL patterns                                 |
| `logging`               | `logToConsole`                                                     | `environments`: `"debug"` (preview only) or `"all"` (production too) |
| `read_data_layer`       | `copyFromDataLayer`                                                | Key match expressions with wildcard support                  |
| `write_data_layer`      | `gtagSet`                                                          | Key match expressions with wildcard support                  |
| `read_container_data`   | `getContainerVersion`                                              | No configuration needed                                      |
| `read_event_metadata`   | `addEventCallback`                                                 | No configuration needed                                      |
| `read_title`            | `readTitle`                                                        | No configuration needed                                      |
| `read_character_set`    | `readCharacterSet`                                                 | No configuration needed                                      |
| `read_analytics_storage`| `readAnalyticsStorage`                                             | No configuration needed                                      |

---

## Sandbox Constraints

### What You CANNOT Do

| Constraint                        | Explanation                                                                 |
|----------------------------------|-----------------------------------------------------------------------------|
| No `window` access               | Cannot reference `window`, `document`, `location`, or any browser globals   |
| No `new` keyword                 | Cannot instantiate objects with `new Date()`, `new RegExp()`, etc.          |
| No `this` keyword                | The `this` binding is not available                                         |
| No global constructors           | `String()`, `Number()`, `Date()`, `RegExp()`, `Array()`, `Object()` are all unavailable |
| No `setTimeout` / `setInterval`  | Use `callLater()` for deferred execution (equivalent to `setTimeout(fn, 0)`) |
| No `Promise` / async-await       | Asynchronous operations use callbacks only                                  |
| No DOM manipulation              | Cannot create, modify, or query DOM elements                                |
| No `eval` / `Function()`         | Dynamic code execution is blocked                                           |
| No `XMLHttpRequest` / `fetch`    | Use `sendPixel` for GET requests or `injectScript` for script loading       |
| No native string/array methods   | Some native prototype methods are removed; use sandboxed equivalents        |
| Limited type system               | Only supports: `null`, `undefined`, `string`, `number`, `boolean`, `array`, `object`, `function` |
| ECMAScript 5.1 base              | Select ES6 features available: arrow functions, `const`, `let`; no destructuring, spread, template literals, classes, modules |

### What You CAN Do

- Use `const`, `let`, arrow functions
- Use object/array literal syntax
- Define and call functions
- Use `for`, `while`, `if/else`, `switch`, ternary operators
- Use all sandboxed APIs via `require()`
- String concatenation with `+`
- Basic arithmetic and comparison operators
- Access template parameters via the `data` object

### Common Patterns

**Pattern: Passing config to an injected script**
```javascript
const setInWindow = require('setInWindow');
const injectScript = require('injectScript');

setInWindow('_myConfig', { key: data.apiKey, debug: data.debugMode }, true);
injectScript('https://cdn.example.com/tracker.js', data.gtmOnSuccess, data.gtmOnFailure, 'tracker');
```

**Pattern: Conditional tracking based on consent**
```javascript
const isConsentGranted = require('isConsentGranted');
const addConsentListener = require('addConsentListener');
const setCookie = require('setCookie');
const sendPixel = require('sendPixel');

if (isConsentGranted('analytics_storage')) {
  setCookie('_track', '1', { domain: 'auto', path: '/', 'max-age': 86400 });
  sendPixel('https://collect.example.com/hit?t=pageview', data.gtmOnSuccess, data.gtmOnFailure);
} else {
  addConsentListener('analytics_storage', (type, granted) => {
    if (granted) {
      sendPixel('https://collect.example.com/hit?t=pageview');
    }
  });
  data.gtmOnSuccess();
}
```

**Pattern: Cache-busted pixel with random number**
```javascript
const sendPixel = require('sendPixel');
const generateRandom = require('generateRandom');
const getTimestampMillis = require('getTimestampMillis');
const encodeUriComponent = require('encodeUriComponent');

const url = 'https://collect.example.com/pixel' +
  '?event=' + encodeUriComponent(data.eventName) +
  '&cb=' + generateRandom(0, 2147483647) +
  '&ts=' + getTimestampMillis();
sendPixel(url, data.gtmOnSuccess, data.gtmOnFailure);
```

**Pattern: Reading and transforming a table parameter**
```javascript
const makeTableMap = require('makeTableMap');
const JSON = require('JSON');
const setInWindow = require('setInWindow');

const paramsMap = makeTableMap(data.customParams, 'key', 'value');
if (paramsMap) {
  setInWindow('_customParams', paramsMap, true);
}
```

---

> **Source:** https://developers.google.com/tag-platform/tag-manager/templates/api
> **Permissions:** https://developers.google.com/tag-platform/tag-manager/templates/permissions
> **Sandbox environment:** https://developers.google.com/tag-platform/tag-manager/templates/sandboxed-javascript
