---
title: GTM Template Builder Plugin
date: 2026-03-28
tags:
  - mwp
---

# GTM Template Builder Plugin — Claude Code Plugin

A Claude Code plugin for building Google Tag Manager custom templates. Provides scaffolding, sandboxed JS API reference, permission generation, parameter design, test writing, and QA review.

## Installation

```bash
claude plugin marketplace add VoxxyCreativeLab/gtm-template-builder-plugin
claude plugin install gtm-template-builder-plugin@gtm-template-builder-plugin
```

After installation, restart Claude Code. All skills are available in every session.

## Skills

| Skill | Command | Description |
|---|---|---|
| Scaffold | `/gtm-template-builder-plugin:scaffold-template [name]` | Create a new GTM template project |
| Sandboxed JS | `/gtm-template-builder-plugin:sandboxed-js` | API reference and patterns |
| Permissions | `/gtm-template-builder-plugin:permissions` | Generate/validate permission blocks |
| Parameters | `/gtm-template-builder-plugin:parameters` | Template parameter types and UI |
| Template Tests | `/gtm-template-builder-plugin:template-tests` | Write and validate tests |
| Gallery Submit | `/gtm-template-builder-plugin:gallery-submit` | Pre-submission checklist |

## Quick Start: Build a New Template

### 1. Scaffold a new project

```
/gtm-template-builder-plugin:scaffold-template MyTracker
```

Walks you through a wizard: pick the third-party tool, event type, architecture, and events to track. Generates `template.tpl`, `metadata.yaml`, `CLAUDE.md`, and `README.md`.

### 2. Write the sandboxed JS

While editing the `___SANDBOXED_JS_FOR_WEB_TEMPLATE___` section of your `.tpl` file, the **sandboxed-js** skill auto-loads. Or invoke it manually:

```
/gtm-template-builder-plugin:sandboxed-js
```

Gives you the full API reference (37+ APIs) — `injectScript`, `setInWindow`, `createQueue`, `sendPixel`, etc. — with constraints and patterns.

### 3. Design the template UI

```
/gtm-template-builder-plugin:parameters
```

Reference for all GTM parameter types: `TEXT`, `SELECT`, `CHECKBOX`, `GROUP`, `PARAM_TABLE`, validators, and UI patterns.

### 4. Generate permissions

```
/gtm-template-builder-plugin:permissions
```

Scans your sandboxed JS `require()` calls and generates the matching `___WEB_PERMISSIONS___` JSON blocks. Also validates existing permissions for correctness.

### 5. Write tests

```
/gtm-template-builder-plugin:template-tests
```

Guides you through writing `___TESTS___` scenarios using `mock()`, `runCode()`, `assertApi()`, and `assertThat()`. Covers 8 test patterns.

### 6. Submit to the Gallery

```
/gtm-template-builder-plugin:gallery-submit
```

Runs the pre-submission checklist: validates `metadata.yaml`, template structure, permissions, tests, and all Gallery requirements.

## QA Agent

The **template-reviewer** agent activates automatically when you're reviewing a `.tpl` file. It checks 50+ items across all template sections: INFO, parameters, sandboxed JS, permissions, tests, and Gallery readiness.

## License

MIT
