---
title: Design Decisions
tags:
  - mwp
  - architecture
---

# Design Decisions

Architecture rationale for gtm-template-builder-plugin. Documents the WHY behind structural choices.

## Skill-per-concern architecture

**Decision:** Each GTM template concern (scaffolding, JS APIs, permissions, parameters, tests, Gallery submission) gets its own skill directory with a dedicated SKILL.md entry point.
**Why:** GTM template authoring involves distinct, separable concerns that users invoke independently. A skill-per-concern split means each skill loads only the reference material it needs, keeping context lean.

## Reference files alongside skills

**Decision:** Each skill's reference material (e.g. `api-reference.md`, `permissions-reference.md`) lives in the same directory as its SKILL.md, not in a shared `_config/` or `reference/` directory.
**Why:** Skills are self-contained units. Co-locating reference files with the skill that uses them means Claude loads exactly what's needed when a skill is invoked, without pulling in unrelated reference material.

## Single agent for QA review

**Decision:** One `template-reviewer` agent handles all QA review, rather than one agent per concern.
**Why:** Template review is inherently cross-cutting — correctness, permissions alignment, test coverage, and Gallery readiness need to be checked together. A single agent avoids redundant scans and ensures nothing falls through gaps between multiple narrow reviewers.

## No pipeline stages

**Decision:** The project uses functional areas (`skills/`, `agents/`) instead of numbered pipeline stages.
**Why:** This is a toolkit, not a sequential workflow. Skills are invoked independently in any order. Imposing pipeline semantics would be artificial and misleading.

## Scaffold-template generates project-level CLAUDE.md

**Decision:** The scaffold-template skill generates a `CLAUDE.md` tailored to each new GTM template project (via `claude-md-template.md`), separate from this plugin's own CLAUDE.md.
**Why:** Each GTM template project has its own identity, rules, and context. The generated CLAUDE.md carries GTM-specific conventions (tag naming, callback requirements, testing expectations) that the plugin-level CLAUDE.md doesn't need.

## Plugin metadata in .claude-plugin/

**Decision:** `plugin.json` and `marketplace.json` live in `.claude-plugin/` (dot-prefixed directory).
**Why:** Claude Code plugin convention. The dot prefix keeps config out of the main project tree while remaining discoverable by the plugin system.
