---
title: Start Here — GTM Template Builder Plugin
tags:
  - mwp
  - onboarding
---

# Start Here — GTM Template Builder Plugin

Welcome. This is a Claude Code plugin for building Google Tag Manager custom templates.

---

## What This Plugin Does

Provides six skills and one agent that cover the full GTM custom template authoring workflow — from scaffolding a new project to preparing it for the Community Template Gallery.

---

## Available Skills

| Skill | Command | What It Does |
|---|---|---|
| Scaffold | `/gtm-template-builder-plugin:scaffold-template` | Create a new GTM template project with .tpl, metadata.yaml, CLAUDE.md, README |
| Sandboxed JS | `/gtm-template-builder-plugin:sandboxed-js` | Full API reference for the GTM sandboxed JS environment |
| Permissions | `/gtm-template-builder-plugin:permissions` | Generate and validate WEB_PERMISSIONS JSON blocks |
| Parameters | `/gtm-template-builder-plugin:parameters` | Template parameter types, UI patterns, configuration |
| Template Tests | `/gtm-template-builder-plugin:template-tests` | Write and validate test scenarios with mock/runCode/assertApi |
| Gallery Submit | `/gtm-template-builder-plugin:gallery-submit` | Pre-submission checklist for the Community Template Gallery |

---

## Available Agent

| Agent | What It Does |
|---|---|
| `template-reviewer` | QA review of .tpl files — correctness, permissions, test coverage, Gallery readiness |

---

## Where Things Live

| I need to... | Go here |
|---|---|
| Understand the plugin | [[CLAUDE]] |
| Find a skill's entry point | `skills/[skill-name]/SKILL.md` |
| See all skills at a glance | [[skills/CONTEXT]] |
| Review the QA agent | [[agents/template-reviewer]] |
| Check technical standards | [[_config/tech-standards]] |
| See project status | [[PLAN]] |

---

## Typical Workflow

1. **Scaffold** — Run `/gtm-template-builder-plugin:scaffold-template` to create a new template project
2. **Build** — Write the sandboxed JS, design parameters, generate permissions
3. **Test** — Write test scenarios and validate
4. **Review** — Run the template-reviewer agent for QA
5. **Submit** — Prepare for Gallery submission

> [!important] Each Skill is Independent
> Skills can be invoked in any order. There is no required pipeline — use what you need, when you need it.
