# GTM Template Parameter Types Reference

## Parameter Types Overview

| Type | Description | `simpleValueType` | Value in `data` |
|------|-------------|-------------------|-----------------|
| `LABEL` | Read-only display text | N/A (no value) | N/A |
| `TEXT` | Single-line text input | `true` | `string` |
| `PARAGRAPH` | Multi-line text area | `true` | `string` |
| `CHECKBOX` | Boolean toggle | `true` | `boolean` |
| `SELECT` | Dropdown menu | `true` | `string` (selected value) |
| `RADIO` | Radio button group | `true` | `string` (selected value) |
| `GROUP` | Collapsible container | N/A (no value) | N/A |
| `SIMPLE_TABLE` | Editable table of rows | N/A | `Array<Object>` |
| `TAG_SELECT` | Select another tag | `true` | Tag reference |

---

## Common Properties (All Types)

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `type` | string | Yes | Parameter type (see table above) |
| `name` | string | Yes | Internal identifier, becomes `data.name` in JS |
| `displayName` | string | Yes | Label shown in GTM UI |

## Optional Properties

| Property | Type | Applies To | Description |
|----------|------|-----------|-------------|
| `simpleValueType` | boolean | TEXT, CHECKBOX, SELECT, RADIO, PARAGRAPH | Must be `true` for primitive-value types |
| `defaultValue` | any | TEXT, CHECKBOX, SELECT, RADIO | Pre-filled value |
| `help` | string | All | Tooltip text shown on hover (?) icon |
| `valueValidators` | array | TEXT, PARAGRAPH | Validation rules array |
| `enablingConditions` | array | All (except GROUP) | Show/hide based on other parameter values |
| `groupStyle` | string | GROUP only | `"ZIPPY_OPEN"` (expanded, collapsible), `"ZIPPY_CLOSED"` (collapsed), or `"NO_ZIPPY"` (flat, no header) |
| `subParams` | array | GROUP only | Nested parameters inside the group |
| `checkboxText` | string | CHECKBOX only | Label next to the checkbox (instead of displayName) |
| `selectItems` | array | SELECT only | Dropdown options |
| `radioItems` | array | RADIO only | Radio button options |
| `macrosInSelect` | boolean | SELECT only | Allow GTM variables in dropdown |

---

## Parameter Types in Detail

### LABEL

Display-only text. No value stored, not accessible in `data`.

```json
{
  "type": "LABEL",
  "name": "infoText",
  "displayName": "This tag tracks user interactions with the widget."
}
```

### TEXT

Single-line text input.

```json
{
  "type": "TEXT",
  "name": "dataLayerEventName",
  "displayName": "DataLayer event name",
  "simpleValueType": true,
  "defaultValue": "my_event",
  "help": "The event name pushed to the dataLayer.",
  "valueValidators": [
    {
      "type": "NON_EMPTY"
    }
  ]
}
```

### PARAGRAPH

Multi-line text area.

```json
{
  "type": "PARAGRAPH",
  "name": "customCode",
  "displayName": "Custom JavaScript",
  "simpleValueType": true,
  "help": "Custom JavaScript code to execute."
}
```

### CHECKBOX

Boolean toggle. Use `checkboxText` for the label next to the checkbox.

```json
{
  "type": "CHECKBOX",
  "name": "enableDeduplication",
  "checkboxText": "Prevent duplicate events per page load",
  "simpleValueType": true,
  "defaultValue": true,
  "help": "When enabled, each event fires only once per page load."
}
```

### SELECT

Dropdown menu with predefined options.

```json
{
  "type": "SELECT",
  "name": "trackingMode",
  "displayName": "Tracking Mode",
  "simpleValueType": true,
  "defaultValue": "standard",
  "selectItems": [
    {
      "value": "standard",
      "displayValue": "Standard"
    },
    {
      "value": "enhanced",
      "displayValue": "Enhanced (includes user details)"
    },
    {
      "value": "minimal",
      "displayValue": "Minimal (event name only)"
    }
  ],
  "help": "Choose how much data to include in dataLayer pushes."
}
```

### RADIO

Radio button group — same as SELECT but displayed as radio buttons.

```json
{
  "type": "RADIO",
  "name": "consentMode",
  "displayName": "Consent Handling",
  "simpleValueType": true,
  "defaultValue": "auto",
  "radioItems": [
    {
      "value": "auto",
      "displayValue": "Automatic (respect GTM consent)"
    },
    {
      "value": "manual",
      "displayValue": "Manual (always fire)"
    }
  ]
}
```

### GROUP

Container for organizing related parameters.

```json
{
  "type": "GROUP",
  "name": "eventSettings",
  "displayName": "Events to track",
  "groupStyle": "ZIPPY_OPEN",
  "subParams": [
    {
      "type": "CHECKBOX",
      "name": "trackPageView",
      "checkboxText": "Page view",
      "simpleValueType": true,
      "defaultValue": true,
      "help": "Fires when a user views a page."
    }
  ]
}
```

**Group styles:**
- `"ZIPPY_OPEN"` — Expanded by default, can be collapsed. **Use for primary settings.** Renders a visible section header bar with expand/collapse toggle and a bordered container. This is the recommended default for top-level groups.
- `"ZIPPY_CLOSED"` — Collapsed by default, click to expand. Use for advanced/optional settings.
- `"NO_ZIPPY"` — Always expanded, no collapse button, no header bar, no visual container. **Only use for subordinate/conditional groups** shown via `enablingConditions`. When used at the top level, subParams can become invisible in GTM's UI because there is no visual boundary or header to distinguish them from surrounding content.

### SIMPLE_TABLE

Editable table where users add/remove rows.

```json
{
  "type": "SIMPLE_TABLE",
  "name": "customParams",
  "displayName": "Custom Parameters",
  "simpleTableColumns": [
    {
      "defaultValue": "",
      "displayName": "Parameter Name",
      "name": "paramName",
      "type": "TEXT"
    },
    {
      "defaultValue": "",
      "displayName": "Parameter Value",
      "name": "paramValue",
      "type": "TEXT"
    }
  ],
  "help": "Add custom key-value pairs to include in the dataLayer push."
}
```

In sandboxed JS, use `makeTableMap` to convert to a key-value object:
```javascript
const makeTableMap = require('makeTableMap');
const params = makeTableMap(data.customParams, 'paramName', 'paramValue');
```

---

## Validators

Add to `valueValidators` array on TEXT and PARAGRAPH parameters.

| Validator | Description | Example |
|-----------|-------------|---------|
| `NON_EMPTY` | Field cannot be blank | `{"type": "NON_EMPTY"}` |
| `POSITIVE_NUMBER` | Must be a positive number | `{"type": "POSITIVE_NUMBER"}` |
| `NON_NEGATIVE_NUMBER` | Must be >= 0 | `{"type": "NON_NEGATIVE_NUMBER"}` |
| `REGEX` | Must match regex pattern | See below |

**Regex validator:**
```json
{
  "type": "REGEX",
  "args": ["^[a-z_]+$"],
  "errorMessage": "Only lowercase letters and underscores allowed."
}
```

---

## Enabling Conditions

Show/hide parameters based on other parameter values. The parameter is only visible when ALL conditions are met.

```json
{
  "type": "TEXT",
  "name": "customEndpoint",
  "displayName": "Custom Endpoint URL",
  "simpleValueType": true,
  "enablingConditions": [
    {
      "paramName": "trackingMode",
      "paramValue": "enhanced",
      "type": "EQUALS"
    }
  ],
  "help": "Only shown when Tracking Mode is set to Enhanced."
}
```

**Condition types:**
- `"EQUALS"` — Show when parameter equals value
- `"NOT_EQUALS"` — Show when parameter does not equal value
- `"PRESENT"` — Show when parameter has any value

**Multiple conditions** (AND logic):
```json
"enablingConditions": [
  {"paramName": "enableAdvanced", "paramValue": true, "type": "EQUALS"},
  {"paramName": "trackingMode", "paramValue": "enhanced", "type": "EQUALS"}
]
```

---

## Common UI Patterns

### Pattern: Event Selection with Checkboxes

Allow users to pick which events to track. Use `ZIPPY_OPEN` so the group has a visible header and bordered container. Do not add a LABEL inside the group — the group's `displayName` already serves as the section header.

```json
{
  "type": "GROUP",
  "name": "eventsToTrack",
  "displayName": "Events to track",
  "groupStyle": "ZIPPY_OPEN",
  "subParams": [
    {
      "type": "CHECKBOX",
      "name": "trackEvent1",
      "checkboxText": "Event name 1",
      "simpleValueType": true,
      "defaultValue": true,
      "help": "Description of event 1."
    },
    {
      "type": "CHECKBOX",
      "name": "trackEvent2",
      "checkboxText": "Event name 2",
      "simpleValueType": true,
      "defaultValue": true,
      "help": "Description of event 2."
    }
  ]
}
```

In sandboxed JS:
```javascript
const allowedEvents = {};
if (data.trackEvent1) allowedEvents.event1 = true;
if (data.trackEvent2) allowedEvents.event2 = true;
```

### Pattern: Basic + Additional Settings

Essential settings visible in a `ZIPPY_OPEN` group, additional collapsed. Global config fields like event name can sit at the top level (outside any group) for immediate visibility.

```json
[
  {
    "type": "TEXT",
    "name": "eventName",
    "displayName": "Event name",
    "simpleValueType": true,
    "defaultValue": "my_event"
  },
  {
    "type": "GROUP",
    "name": "basicSettings",
    "displayName": "Settings",
    "groupStyle": "ZIPPY_OPEN",
    "subParams": [...]
  },
  {
    "type": "GROUP",
    "name": "additionalSettings",
    "displayName": "Additional settings",
    "groupStyle": "ZIPPY_CLOSED",
    "subParams": [...]
  }
]
```

> **Do NOT use "Advanced Settings" as the displayName.** Google's tag UI adds its own built-in "Advanced Settings" section to every tag, so using that name creates a confusing duplicate.

> **Use sentence case for all labels.** Per Google's GTM template style guide: capitalize the first word only, except for proper nouns. Example: "Additional settings", not "Additional Settings".

### Pattern: Conditional Fields

Show extra fields only when a specific option is selected:

```json
[
  {
    "type": "SELECT",
    "name": "source",
    "displayName": "Script Source",
    "simpleValueType": true,
    "defaultValue": "cdn",
    "selectItems": [
      {"value": "cdn", "displayValue": "CDN (recommended)"},
      {"value": "custom", "displayValue": "Custom URL"}
    ]
  },
  {
    "type": "TEXT",
    "name": "customUrl",
    "displayName": "Custom Script URL",
    "simpleValueType": true,
    "enablingConditions": [
      {"paramName": "source", "paramValue": "custom", "type": "EQUALS"}
    ],
    "valueValidators": [{"type": "NON_EMPTY"}]
  }
]
```

### Pattern: Description Label at Top

Add context above the form:

```json
[
  {
    "type": "LABEL",
    "name": "description",
    "displayName": "This tag listens for Widget events and pushes them to the dataLayer. Configure the options below to control which events are tracked."
  },
  ...
]
```
