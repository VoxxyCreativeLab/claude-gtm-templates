---
title: Skills — Directory Index
tags:
  - mwp
  - layer-1
---

# Skills — Directory Index

%% LAYER 1 — ROUTING %%
%% This file indexes all skills in the plugin. Each skill has its own subdirectory with a SKILL.md entry point. %%

## Skill Index

| Skill | Directory | Entry Point | Reference Files |
|---|---|---|---|
| Scaffold Template | `scaffold-template/` | [[scaffold-template/SKILL\|SKILL.md]] | `tpl-skeleton.md`, `claude-md-template.md` |
| Sandboxed JS | `sandboxed-js/` | [[sandboxed-js/SKILL\|SKILL.md]] | `api-reference.md` |
| Permissions | `permissions/` | [[permissions/SKILL\|SKILL.md]] | `permissions-reference.md` |
| Parameters | `parameters/` | [[parameters/SKILL\|SKILL.md]] | `parameter-types.md` |
| Template Tests | `template-tests/` | [[template-tests/SKILL\|SKILL.md]] | `test-examples.md` |
| Gallery Submit | `gallery-submit/` | [[gallery-submit/SKILL\|SKILL.md]] | `metadata-yaml.md` |

## Skill Structure Pattern

Each skill directory follows this structure:

```
[skill-name]/
  SKILL.md              ← Entry point — loaded when the skill is invoked
  [reference-file].md   ← Supporting reference material (Layer 3)
```

## Adding a New Skill

1. Create a new directory under `skills/`
2. Add a `SKILL.md` with frontmatter including `name`, `description`, and trigger conditions
3. Add supporting reference files as needed
4. Update this index table
5. Update [[../CLAUDE|CLAUDE.md]] and [[../README|README.md]] skill tables
