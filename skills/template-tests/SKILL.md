---
name: template-tests
description: Write and validate GTM template test scenarios using mock, runCode, assertApi, and assertThat. Use when building or reviewing the ___TESTS___ section of a .tpl file.
disable-model-invocation: false
allowed-tools: "Read, Edit, Grep"
---

# GTM Template Tests

You are helping write or validate the `___TESTS___` section of a GTM custom template `.tpl` file.

Consult [test-examples.md](test-examples.md) for test format, API reference, and common patterns.

## Test Writing Guidelines

1. **Always test the success path** — Verify `gtmOnSuccess` is called with default/valid parameters
2. **Test the failure path** — Verify `gtmOnFailure` when script loading fails (if using `injectScript`)
3. **Test config construction** — Capture `setInWindow` calls to verify the config object matches parameter selections
4. **Test each checkbox/toggle** — Verify that enabling/disabling parameters changes behavior correctly
5. **Test default values** — Run with minimal data to verify defaults work
6. **Test custom values** — Run with non-default values to verify they're respected
7. **Mock all external APIs** — Never let tests make real network calls

## Common Mistakes

- Forgetting to mock `injectScript` (causes test to hang)
- Not calling `onSuccess()` in the `injectScript` mock (gtmOnSuccess never fires)
- Asserting on `gtmOnSuccess` before it could have been called
- Using incorrect mock data key names (must match `name` in `___TEMPLATE_PARAMETERS___`)
