---
name: parameters
description: GTM template parameter types, UI patterns, and configuration reference. Use when designing the ___TEMPLATE_PARAMETERS___ section of a .tpl file.
disable-model-invocation: false
---

# GTM Template Parameters

You are helping design the `___TEMPLATE_PARAMETERS___` section of a GTM custom template `.tpl` file. This section defines the UI that users see when configuring a tag in GTM.

Consult [[parameter-types]] for the complete reference of all parameter types, properties, validators, and UI patterns.

## Key Principles

1. **Keep it simple** — Only expose settings users actually need. Use sensible defaults.
2. **Group logically** — Use `GROUP` with `ZIPPY_OPEN` for primary settings, `ZIPPY_CLOSED` for advanced/optional. Only use `NO_ZIPPY` for subordinate/conditional groups shown via `enablingConditions`.
3. **Top-level globals** — Place global config params (like event name) at the top level, not inside groups, so they're immediately visible.
4. **Add help text** — Every parameter should have a `help` tooltip explaining what it does.
5. **Validate input** — Use `valueValidators` (especially `NON_EMPTY`) on required text fields.
6. **No redundant labels** — Don't add LABEL params inside GROUPs; the GROUP `displayName` is the section header.
7. **Sentence case** — All `displayName` and `checkboxText` values must use sentence case (capitalize first word only, except proper nouns).
8. **Match parameter names to code** — The `name` field becomes `data.paramName` in sandboxed JS.

## Quick Example

A well-structured parameter section for a tracking template:

```json
[
  {
    "type": "TEXT",
    "name": "dataLayerEventName",
    "displayName": "DataLayer event name",
    "simpleValueType": true,
    "defaultValue": "my_event",
    "help": "The event name pushed to the dataLayer when tracking fires.",
    "valueValidators": [{ "type": "NON_EMPTY" }]
  },
  {
    "type": "GROUP",
    "name": "eventSettings",
    "displayName": "Event settings",
    "groupStyle": "ZIPPY_OPEN",
    "subParams": [
      {
        "type": "CHECKBOX",
        "name": "trackClicks",
        "checkboxText": "Track click events",
        "simpleValueType": true,
        "defaultValue": true
      }
    ]
  }
]
```

Global config (`dataLayerEventName`) is at top level. Related toggles are grouped. Sentence case throughout.

## Common Mistakes

- **LABEL inside a GROUP** — the GROUP `displayName` is already the section header; adding a LABEL is redundant
- **Missing `valueValidators`** — required TEXT fields without `NON_EMPTY` let users save blank values, causing silent failures
- **`ZIPPY_OPEN` for advanced settings** — use `ZIPPY_CLOSED` for optional/advanced groups so they don't clutter the UI
- **camelCase in `displayName`** — use sentence case ("Track click events", not "Track Click Events")
