---
name: parameters
description: GTM template parameter types, UI patterns, and configuration reference. Use when designing the ___TEMPLATE_PARAMETERS___ section of a .tpl file.
disable-model-invocation: false
---

# GTM Template Parameters

You are helping design the `___TEMPLATE_PARAMETERS___` section of a GTM custom template `.tpl` file. This section defines the UI that users see when configuring a tag in GTM.

Consult [parameter-types.md](parameter-types.md) for the complete reference of all parameter types, properties, validators, and UI patterns.

## Key Principles

1. **Keep it simple** — Only expose settings users actually need. Use sensible defaults.
2. **Group logically** — Use `GROUP` with `NO_ZIPPY` for essential settings, `ZIPPY_CLOSED` for advanced.
3. **Add help text** — Every parameter should have a `help` tooltip explaining what it does.
4. **Validate input** — Use `valueValidators` (especially `NON_EMPTY`) on required text fields.
5. **Use descriptive labels** — Add `LABEL` parameters above sections to explain what follows.
6. **Match parameter names to code** — The `name` field becomes `data.paramName` in sandboxed JS.
