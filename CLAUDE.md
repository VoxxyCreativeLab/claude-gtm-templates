---
title: GTM Template Builder Plugin
tags:
  - mwp
  - layer-0
---

# GTM Template Builder Plugin

A Claude Code plugin providing a complete toolkit for authoring GTM custom templates — scaffolding, sandboxed JS API reference, permission generation, parameter design, test writing, and Gallery submission prep.

## Who You Are Helping

A tracking specialist with deep GTM/sGTM expertise, building and maintaining Claude Code plugins for the GTM template development workflow.

## Permanent Rules

- Each skill directory contains a `SKILL.md` (entry point) and supporting reference files
- Agent definitions live in `agents/` as standalone markdown files
- Layer 3 reference files in `_config/` are stable — do not overwrite during a run
- Plugin configuration in `.claude-plugin/` must stay in sync with README.md skill table
- No stale commits allowed

## Companion Tools

| Plugin / Tool | Relationship |
|---|---|
| `project-structure-and-scaffolding-plugin` | Master plugin — MWP structure and scaffolding |
| `gtm-template-builder-plugin` | This plugin — GTM template authoring toolkit |

> [!info] Ecosystem
> See [[_config/ecosystem]] for the full list of available plugins, CLIs, and MCPs.

## Root Files

| File | Role |
|------|------|
| `CLAUDE.md` | This file. Permanent identity and rules. Does not change. |
| `CONTEXT.md` | Task routing. Which files to load for a given task. |
| `PRIMER.md` | Living session handoff. Rewrites at end of every session. |
| `MEMORY.sh` | Injects live git context at launch. |
| `LESSONS.md` | Domain lessons learned. Grows over time. |
| `DESIGN.md` | Architecture decisions and rationale. |
| `CHANGELOG.md` | Versioned change log. |
