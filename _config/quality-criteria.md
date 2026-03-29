---
title: Quality Criteria
tags:
  - layer-3
  - reference
---

# Quality Criteria

%% LAYER 3 — STABLE REFERENCE %%
%% Defines what "done" means for outputs in this plugin project. %%
%% Claude uses this to self-evaluate before finalizing any skill or agent output. %%

## Definition of Done

A skill or agent output is "done" when:
- [ ] All required sections are populated with real content (no placeholder brackets remaining)
- [ ] GTM template code calls `data.gtmOnSuccess()` and `data.gtmOnFailure()`
- [ ] Permission blocks match the sandboxed JS APIs actually used
- [ ] Test scenarios cover success, failure, and edge cases
- [ ] [Add further conditions as needed]

## Quality Signals (What Good Looks Like)

%% HOW TO FILL THIS: Give 2-3 concrete examples of high-quality output for THIS project. %%

- [Quality signal 1 — describe what a strong skill output looks like]
- [Quality signal 2]
- [Quality signal 3]

## Rejection Criteria (What Gets Sent Back)

Output is not acceptable and must be revised if:
- [ ] It contains unfilled placeholder text (`[brackets]` or generic filler)
- [ ] GTM template is missing required sections (INFO, PARAMETERS, JS, PERMISSIONS, TESTS)
- [ ] Permission blocks declare access not used by the template code
- [ ] Tests do not exercise the main code paths
- [ ] [Add further rejection criteria as needed]

## Component-Specific Overrides

| Component | Additional or overriding criteria |
|---|---|
| scaffold-template | Must generate all four files: .tpl, metadata.yaml, CLAUDE.md, README.md |
| gallery-submit | Must validate metadata.yaml against Gallery requirements |
| template-reviewer | Must check all 5 template sections + permissions alignment |
