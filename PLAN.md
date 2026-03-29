---
title: Plan — GTM Template Builder Plugin
date: 2026-03-28
tags:
  - mwp
  - plan
---

# Plan — GTM Template Builder Plugin

**Last updated:** 2026-03-28
**Current milestone:** Maintenance & iteration

---

## Project Overview

A Claude Code plugin providing a complete toolkit for GTM custom template development. The plugin is active and published, with six skills and one agent covering scaffolding, JS reference, permissions, parameters, testing, and Gallery submission.

---

## Components & Status

| Component | Type | Status | Notes |
|-----------|------|--------|-------|
| scaffold-template | Skill | ✅ Done | Project scaffolding wizard |
| sandboxed-js | Skill | ✅ Done | API reference (37+ APIs) |
| permissions | Skill | ✅ Done | WEB_PERMISSIONS generation |
| parameters | Skill | ✅ Done | Parameter types + UI patterns |
| template-tests | Skill | ✅ Done | Test scenario writing |
| gallery-submit | Skill | ✅ Done | Gallery submission prep |
| template-reviewer | Agent | ✅ Done | QA review agent |

**Status key:** ⬜ Not started · 🔄 In progress · ✅ Done · ⚠️ Blocked

---

## Current Focus

**Active work:** MWP structure retrofit
**What's happening:** Applying MWP meta-files to the existing plugin structure
**Blockers:** None

---

## Decisions Made

- 2026-03-28 — Applied MWP structure via overlay mode (no pipeline stages — functional areas only)
- 2026-03-28 — Master plugin: project-structure-and-scaffolding-plugin

---

## Next Steps

1. Fill in Layer 3 stubs in `_config/` with plugin-specific standards
2. Review and update each skill's SKILL.md as needed
3. Continue plugin maintenance and iteration

---

## Notes / Open Questions

- Plugin is published and stable — MWP structure added for consistency with the plugin ecosystem
