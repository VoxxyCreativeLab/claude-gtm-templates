---
title: Conventions
tags:
  - layer-3
  - reference
---

# Conventions

%% LAYER 3 — STABLE REFERENCE %%
%% Naming, file organisation, and structural rules for this plugin project. %%

## File Naming

%% HOW TO FILL THIS: Define what your files will be called and how. %%

- Skill entry points: always `SKILL.md` (uppercase)
- Reference files: lowercase, hyphen-separated (e.g. `api-reference.md`, `parameter-types.md`)
- Agent definitions: lowercase, hyphen-separated (e.g. `template-reviewer.md`)
- Plugin config: lowercase (e.g. `plugin.json`, `marketplace.json`)

## Folder Naming

- Skill directories: lowercase, hyphen-separated (e.g. `scaffold-template`, `sandboxed-js`)
- Config directory: `_config/` (underscore prefix per MWP convention)
- Plugin config: `.claude-plugin/` (dot prefix for hidden/config dirs)

## Output Conventions

%% HOW TO FILL THIS: Clarify what the skills produce for end users. %%

- Skills produce GTM template projects in the user's working directory (not in this plugin)
- This plugin itself has no `output/` directories — it is a toolkit, not a pipeline

## Version Control

- Commit after meaningful changes to skills or agents
- Commit message format: `[area]: [brief description]` (e.g. `skills/permissions: add sendPixel API`)
- Tag releases: `v[major].[minor].[patch]`

## Review & Handoff

- Changes to SKILL.md files should be tested by invoking the skill
- Changes to reference files should be reviewed for accuracy against GTM documentation
- Plugin version in `plugin.json` must be bumped on release

## Project-Specific Conventions

%% HOW TO FILL THIS: Any convention unique to this project. %%

- [Convention specific to this project]
- [Convention specific to this project]
