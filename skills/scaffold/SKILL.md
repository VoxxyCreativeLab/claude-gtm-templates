---
name: scaffold
description: Scaffold a new GTM custom template project with .tpl, metadata.yaml, CLAUDE.md, and README
argument-hint: "[template-name]"
disable-model-invocation: true
allowed-tools: "Read, Write, Bash, Glob"
---

# Scaffold a New GTM Custom Template Project

You are creating a new GTM (Google Tag Manager) custom template project. The template name is `$ARGUMENTS`.

## Steps

1. **Ask the user** what this template will track and how:
   - What third-party tool/widget does it track? (e.g., Calendly, Drift, Typeform, Intercom)
   - What event mechanism does the tool use? (postMessage, custom events, callbacks, DOM mutations)
   - Will it need an external helper script (injectScript pattern) or can it work entirely in sandboxed JS?
   - What events should be tracked?

2. **Create the project directory** with these files:

### `template.tpl`
Use the skeleton from [tpl-skeleton.md](tpl-skeleton.md). Fill in:
- `___INFO___`: Set displayName, description, categories, brand (Voxxy Creative Lab)
- `___TEMPLATE_PARAMETERS___`: Based on user's answers — at minimum a dataLayer event name TEXT field and event selection CHECKBOXes
- `___SANDBOXED_JS_FOR_WEB_TEMPLATE___`: Stub with require() calls, data reading, and either injectScript or direct logic
- `___WEB_PERMISSIONS___`: Match the APIs used in the sandboxed JS
- `___TESTS___`: At least one basic test scenario
- `___NOTES___`: Creation date and author

### `metadata.yaml`
```yaml
homepage: "https://github.com/VoxxyCreativeLab/template-[name]"
documentation: "https://github.com/VoxxyCreativeLab/template-[name]/blob/main/README.md"
versions: []
```

### `CLAUDE.md`
Use the template from [claude-md-template.md](claude-md-template.md). Fill in project-specific details.

### `README.md`
Create a user-facing README with:
- Template name and description
- What it tracks
- Installation instructions (import into GTM)
- Configuration options (matching template parameters)
- DataLayer output format
- Testing instructions

### `.gitignore`
```
node_modules/
.DS_Store
Thumbs.db
*.log
```

### `LICENSE`
MIT license with current year and "Voxxy Creative Lab" as copyright holder. Unless the user specifies a different license.

3. **Initialize git** in the project directory

4. **Summarize** what was created and what the user should do next (fill in template logic, push to GitHub, test in GTM)
