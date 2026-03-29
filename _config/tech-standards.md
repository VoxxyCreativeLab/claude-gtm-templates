---
title: Technical Standards
tags:
  - layer-3
  - reference
---

# Technical Standards

%% LAYER 3 — STABLE REFERENCE %%
%% Defines stack, tooling, code conventions, and technical constraints for this plugin. %%

## Stack

- Platform: Claude Code plugin (markdown-based skill/agent system)
- Target: Google Tag Manager custom templates (.tpl files)
- Runtime: GTM sandboxed JavaScript environment
- Package manager: N/A (no build step — pure markdown + template files)

## Code Conventions

%% HOW TO FILL THIS: Rules Claude follows when writing any code or structured output for this project. %%

- Indentation: [2 spaces / 4 spaces / tabs]
- Quotes: [Single / Double]
- GTM template sections: `___INFO___`, `___TEMPLATE_PARAMETERS___`, `___SANDBOXED_JS_FOR_WEB_TEMPLATE___`, `___WEB_PERMISSIONS___`, `___TESTS___`
- Naming: [conventions for skill names, file names, etc.]

## Architecture Patterns

%% HOW TO FILL THIS: Structural decisions Claude must respect. %%

- Each skill is a self-contained directory with `SKILL.md` as entry point
- Reference files (Layer 3) live alongside the SKILL.md that uses them
- Agents are standalone markdown files in `agents/`
- Plugin metadata lives in `.claude-plugin/` (plugin.json, marketplace.json)

## Component/Module Structure

```
skills/[skill-name]/
  SKILL.md              ← Entry point — loaded when skill is invoked
  [reference-file].md   ← Supporting reference material

agents/
  [agent-name].md       ← Agent definition
```

## Testing

%% HOW TO FILL THIS: Testing rules for this project. %%

- Test runner: [e.g. manual review / template-reviewer agent]
- Coverage target: [e.g. all skills must have reference files]
- Test naming: [conventions]

## Build & Deploy

- No build step — plugin is deployed as-is via marketplace
- Install: `claude plugin marketplace add VoxxyCreativeLab/gtm-template-builder-plugin`
- Versioning: managed in `.claude-plugin/plugin.json`

## Project-Specific Technical Rules

- All GTM templates must call `data.gtmOnSuccess()` / `data.gtmOnFailure()`
- Sandboxed JS APIs must be referenced from the official GTM API list
- Permission blocks must match the APIs actually used in the template code
