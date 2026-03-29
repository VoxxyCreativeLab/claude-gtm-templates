---
title: GTM Template Builder Plugin
tags:
  - mwp
  - layer-0
---

# GTM Template Builder Plugin

%% LAYER 0 — GLOBAL IDENTITY %%

## What This Is

A Claude Code plugin that provides a complete toolkit for authoring GTM custom templates — including project scaffolding, sandboxed JS API reference, permission block generation, parameter UI design, test writing, and Community Template Gallery submission prep.

This workspace follows MWP (Model Workspace Protocol) conventions. Plain markdown files carry context. Skills and agents are organized in functional directories.

---

## Folder Structure

```
gtm-template-builder-plugin/
  CLAUDE.md             ← You are here. Global identity.
  CONTEXT.md            ← Task routing. Where to go for what.
  README.md             ← Human-facing plugin overview + installation
  START-HERE.md         ← Quick-start guide
  PLAN.md               ← Project roadmap
  _config/              ← Layer 3: Stable reference files
  .claude-plugin/       ← Plugin configuration (marketplace.json, plugin.json)
  skills/               ← Skill definitions (6 skills)
    gallery-submit/
    parameters/
    permissions/
    sandboxed-js/
    scaffold-template/
    template-tests/
  agents/               ← Agent definitions (template-reviewer)
```

---

## Quick Navigation

| I want to... | Go here |
|---|---|
| Understand a skill | Read its `SKILL.md` in `skills/[skill-name]/` |
| Review the template reviewer agent | [[agents/template-reviewer]] |
| See all available skills | [[skills/CONTEXT]] |
| See all available agents | [[agents/CONTEXT]] |
| Adjust conventions | [[_config/conventions]] |
| Check technical standards | [[_config/tech-standards]] |
| See what's in progress | [[PLAN]] |

---

## Active Plugins & Domain Tools

| Plugin / Tool | Relationship |
|---|---|
| `project-structure-and-scaffolding-plugin` | Master plugin — MWP structure and scaffolding |
| `gtm-template-builder-plugin` | This plugin — GTM template authoring toolkit |

> [!info] Ecosystem
> See [[_config/ecosystem]] for the full list of available plugins, CLIs, and MCPs.

---

## Rules That Apply Everywhere

- Each skill directory contains a `SKILL.md` (entry point) and supporting reference files
- Agent definitions live in `agents/` as standalone markdown files
- Layer 3 reference files in `_config/` are stable — do not overwrite during a run
- Plugin configuration in `.claude-plugin/` must stay in sync with README.md skill table
