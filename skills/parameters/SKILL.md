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
2. **Group logically** — Use `GROUP` with `ZIPPY_OPEN` for primary settings, `ZIPPY_CLOSED` for advanced/optional. Only use `NO_ZIPPY` for subordinate/conditional groups shown via `enablingConditions`.
3. **Top-level globals** — Place global config params (like event name) at the top level, not inside groups, so they're immediately visible.
4. **Add help text** — Every parameter should have a `help` tooltip explaining what it does.
5. **Validate input** — Use `valueValidators` (especially `NON_EMPTY`) on required text fields.
6. **No redundant labels** — Don't add LABEL params inside GROUPs; the GROUP `displayName` is the section header.
7. **Sentence case** — All `displayName` and `checkboxText` values must use sentence case (capitalize first word only, except proper nouns).
8. **Match parameter names to code** — The `name` field becomes `data.paramName` in sandboxed JS.
