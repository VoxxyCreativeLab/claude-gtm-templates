# GTM Template Skeleton (.tpl)

Use this skeleton when scaffolding a new GTM custom template. Replace all `[PLACEHOLDER]` values.

```
___INFO___

{
  "type": "TAG",
  "id": "cvt_temp_public_id",
  "version": 1,
  "securityGroups": [],
  "displayName": "[Template Display Name]",
  "categories": [
    "[CATEGORY1]"
  ],
  "brand": {
    "id": "brand_dummy",
    "displayName": "Voxxy Creative Lab",
    "thumbnail": ""
  },
  "description": "[Brief description of what this template does]",
  "containerContexts": [
    "WEB"
  ]
}


___TEMPLATE_PARAMETERS___

[
  {
    "type": "LABEL",
    "name": "description",
    "displayName": "[Description text shown at top of template configuration]"
  },
  {
    "type": "GROUP",
    "name": "eventSettings",
    "displayName": "Event Settings",
    "groupStyle": "NO_ZIPPY",
    "subParams": [
      {
        "type": "TEXT",
        "name": "dataLayerEventName",
        "displayName": "DataLayer Event Name",
        "simpleValueType": true,
        "defaultValue": "[default_event_name]",
        "help": "The event name pushed to the dataLayer. Default: '[default_event_name]'.",
        "valueValidators": [
          {
            "type": "NON_EMPTY"
          }
        ]
      }
    ]
  },
  {
    "type": "GROUP",
    "name": "additionalSettings",
    "displayName": "Additional Settings",
    "groupStyle": "ZIPPY_CLOSED",
    "subParams": [
      {
        "type": "CHECKBOX",
        "name": "enableDebugLogging",
        "checkboxText": "Enable debug logging in browser console",
        "simpleValueType": true,
        "defaultValue": false,
        "help": "When enabled, logs detailed tracking information to the browser console. Useful for verifying which events are being captured and troubleshooting. Disable in production."
      }
    ]
  }
]


___SANDBOXED_JS_FOR_WEB_TEMPLATE___

// Require GTM sandboxed APIs
const logToConsole = require('logToConsole');

// Read template parameter values
const dataLayerEventName = data.dataLayerEventName || '[default_event_name]';

// Debug logging helper
if (data.enableDebugLogging) {
  logToConsole('[TemplateName] Initializing with event name: ' + dataLayerEventName);
}

// TODO: Implement template logic

// Signal completion
data.gtmOnSuccess();


___WEB_PERMISSIONS___

[
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
]


___TESTS___

scenarios:
- name: Basic test - tag executes successfully
  code: |-
    const mockData = {
      dataLayerEventName: '[default_event_name]',
      enableDebugLogging: false
    };

    runCode(mockData);
    assertApi('gtmOnSuccess').wasCalled();


___NOTES___

Created on [DATE] by Voxxy Creative Lab.
```

## Valid Categories

Use one or more of these in the `categories` array:

- `"ANALYTICS"` — Analytics and measurement
- `"CONVERSIONS"` — Conversion tracking
- `"REMARKETING"` — Remarketing and audience building
- `"TAG_MANAGEMENT"` — Tag management utilities
- `"ADVERTISING"` — Ad platforms
- `"AFFILIATE_MARKETING"` — Affiliate marketing
- `"ATTRIBUTION"` — Attribution and cross-channel
- `"CHAT"` — Chat and messaging tools
- `"DATA_WAREHOUSING"` — Data warehousing integrations
- `"EMAIL_MARKETING"` — Email marketing
- `"ENGAGEMENT"` — User engagement tracking
- `"EXPERIMENTATION"` — A/B testing and experimentation
- `"HEAT_MAP"` — Heatmap and session recording
- `"PERSONALIZATION"` — Personalization
- `"SOCIAL"` — Social media
- `"SURVEY"` — Survey and feedback tools
- `"UTILITY"` — General purpose utility

## Template Types

- `"TAG"` — Tag templates (most common)
- `"VARIABLE"` — Variable templates (return a value)
