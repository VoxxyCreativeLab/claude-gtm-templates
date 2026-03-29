---
title: GTM Template Builder Plugin — Task Router
tags:
  - mwp
  - layer-1
---

# GTM Template Builder Plugin — Task Router

%% LAYER 1 — ROUTING %%

## What to Load

| Task | Go Here | Load These |
|------|---------|-----------|
| Scaffold a new GTM template | `skills/scaffold-template/` | [[skills/scaffold-template/SKILL\|SKILL.md]] |
| Write sandboxed JS code | `skills/sandboxed-js/` | [[skills/sandboxed-js/SKILL\|SKILL.md]] + `api-reference.md` |
| Generate permission blocks | `skills/permissions/` | [[skills/permissions/SKILL\|SKILL.md]] + `permissions-reference.md` |
| Design template parameters | `skills/parameters/` | [[skills/parameters/SKILL\|SKILL.md]] + `parameter-types.md` |
| Write template tests | `skills/template-tests/` | [[skills/template-tests/SKILL\|SKILL.md]] + `test-examples.md` |
| Prepare Gallery submission | `skills/gallery-submit/` | [[skills/gallery-submit/SKILL\|SKILL.md]] + `metadata-yaml.md` |
| Review a template (QA) | `agents/` | [[agents/template-reviewer]] |
| Check conventions or quality | `_config/` | Relevant stub file |

---

## Shared Resources

Resources available to all skills and agents:

- [[_config/tech-standards]] — Stack, code conventions, architecture patterns
- [[_config/conventions]] — File/folder naming, output conventions
- [[_config/quality-criteria]] — Definition of done, quality signals, rejection criteria
- [[_config/ecosystem]] — Full plugin/CLI/MCP ecosystem reference

---

## Structure Map

```
skills/
  scaffold-template/  → Project scaffolding (tpl, metadata, CLAUDE.md, README)
  sandboxed-js/       → Sandboxed JS API reference (37+ APIs)
  permissions/        → WEB_PERMISSIONS block generation
  parameters/         → TEMPLATE_PARAMETERS design
  template-tests/     → Test scenario writing (mock, runCode, assertApi)
  gallery-submit/     → Community Template Gallery submission prep

agents/
  template-reviewer   → QA review agent for .tpl files
```
