---
name: gallery-submit
description: Prepare a GTM custom template for Community Template Gallery submission. Validates metadata.yaml, template structure, and Gallery requirements.
disable-model-invocation: true
allowed-tools: "Read, Bash, Grep, Glob"
---

# Prepare for GTM Community Template Gallery Submission

You are helping prepare a GTM custom template for submission to the [Community Template Gallery](https://tagmanager.google.com/gallery/).

Consult [metadata-yaml.md](metadata-yaml.md) for the metadata.yaml format.

## Pre-Submission Checklist

Run through each item and report pass/fail:

### 1. Repository Structure
- [ ] `template.tpl` exists at repo root
- [ ] `metadata.yaml` exists at repo root
- [ ] Repository is public on GitHub

### 2. `___INFO___` Section
- [ ] Valid JSON
- [ ] `displayName` is set (this becomes the template name in the Gallery)
- [ ] `description` is set (shown in search results)
- [ ] `categories` contains at least one valid category
- [ ] `brand.displayName` is set
- [ ] `containerContexts` is set (usually `["WEB"]`)
- [ ] `type` is either `"TAG"` or `"VARIABLE"`

### 3. `___TEMPLATE_PARAMETERS___`
- [ ] Valid JSON array
- [ ] All parameters have `name` and `displayName`
- [ ] Required fields have `valueValidators` with `NON_EMPTY`

### 4. `___SANDBOXED_JS_FOR_WEB_TEMPLATE___`
- [ ] Code is present
- [ ] Calls `data.gtmOnSuccess()` on success path
- [ ] Calls `data.gtmOnFailure()` on failure path (if applicable)
- [ ] No forbidden APIs (eval, Function constructor, etc.)

### 5. `___WEB_PERMISSIONS___`
- [ ] Valid JSON array
- [ ] Every `require()` in sandboxed JS has a matching permission
- [ ] No unused permissions (permissions without corresponding `require()`)

### 6. `___TESTS___`
- [ ] At least one test scenario exists
- [ ] Tests cover the success path
- [ ] Tests cover the failure path (if using injectScript)

### 7. `metadata.yaml`
- [ ] `homepage` URL is valid and points to the repo
- [ ] `documentation` URL is valid and points to README
- [ ] `versions` array has at least one entry
- [ ] Latest version `sha` matches a real commit on the default branch
- [ ] `changeNotes` describes the version

### 8. Documentation
- [ ] README.md exists with installation instructions
- [ ] README explains what the template does
- [ ] README lists configuration options

## After Validation

If all checks pass, provide:
1. The submission URL: https://github.com/nickreese/community-templates/blob/main/README.md
2. Remind the user to verify the SHA in `metadata.yaml` matches the latest commit
3. Remind them to test one final time in GTM Preview mode before submitting
