# metadata.yaml Format Reference

## Purpose

The `metadata.yaml` file is required for templates submitted to the GTM Community Template Gallery. It tells the Gallery where to find the template and which versions are available.

## Format

```yaml
homepage: "https://github.com/YourOrg/template-name"
documentation: "https://github.com/YourOrg/template-name/blob/main/README.md"
versions:
  - sha: "full_40_char_commit_sha_here"
    changeNotes: "v1.0.0 - Initial release with feature X and Y"
```

## Fields

| Field | Required | Description |
|-------|----------|-------------|
| `homepage` | Yes | URL to the template's repository or homepage |
| `documentation` | Yes | URL to the template's documentation (usually README) |
| `versions` | Yes | Array of version objects, newest first |
| `versions[].sha` | Yes | Full 40-character commit SHA from the default branch |
| `versions[].changeNotes` | Yes | Human-readable release description |

## Rules

1. **`sha` must be a real commit** on the default branch (main/master). The Gallery fetches the `.tpl` file from this exact commit.
2. **Newest version first** — The Gallery uses the first entry as the latest version.
3. **`changeNotes`** should include a version number and brief description.
4. **URLs must be public** — The Gallery needs to access these URLs.

## Getting the SHA

```bash
# Get SHA of the latest commit
git rev-parse HEAD

# Get SHA of a specific tag
git rev-parse v1.0.0

# Get SHA of a specific commit message
git log --oneline | head -5
```

## Example with Multiple Versions

```yaml
homepage: "https://github.com/VoxxyCreativeLab/template-calendly"
documentation: "https://github.com/VoxxyCreativeLab/template-calendly/blob/main/README.md"
versions:
  - sha: c3b86bf3aa627ae29e9fcdbaa8b29cabfd8c5280
    changeNotes: "v1.0.0 - Update UI labels and add post-template screenshots"
  - sha: ea3229d56ea243d0f3126e3a95730729418c941e
    changeNotes: "Initial release - Calendly event tracking with filtering and deduplication"
```

## Adding a New Version

When releasing a new version:
1. Commit and push your changes to the default branch
2. Get the new commit SHA: `git rev-parse HEAD`
3. Add a new entry at the **top** of the `versions` array
4. Commit the updated `metadata.yaml`
5. Update the SHA to point to this final commit (since `metadata.yaml` itself changed)
6. Push and verify
