# smart-copy-cat: Copy Changed Files with Path Transformation

This GitHub Action copies modified files from the triggering repository to another repository while allowing path transformation, and automatically creates a pull request with the changes.

## Features

- Copies only changed files from push/pull_request events
- Allows path pattern replacement (prune and insert)
- Preserves directory structure (with optional transformations)
- Automatically creates pull requests in target repository
- Handles both initial commits and subsequent updates
- Works with both push and pull_request triggers

```yaml
name: Sync Files to Another Repository

on:
  push:
    branches: [ main ]
    paths:
      - 'path/**'  # Only trigger when files in this path change

jobs:
  copy-files:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout current repo
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Required for git diff to work

      - name: Copy changed files to target repo
        uses: ItsGiantCookie/smart-copy-cat@v1  # Replace with your actual action reference
        with:
          output-repo: 'organization/target-repo'
          output-branch: 'release'
          path-pattern-to-prune: 'path/'
          path-pattern-to-insert: 'path/'
          output-directory: 'imported-assets'
          commit-message-prefix: '[Auto-Sync] '
          pr-title: 'Automated update: assets'
          pr-message: 'Syncing latest changes from development branch'
          git-token: ${{ secrets.REPO_SYNC_TOKEN }}
```

## Inputs

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `output-repo` | Yes | - | Target repository (format: `owner/repo`) |
| `output-branch` | Yes | - | Target branch for PR |
| `path-pattern-to-prune` | No | '' | Path segment to remove (e.g., `Path/File`) |
| `path-pattern-to-insert` | No | '' | Replacement path segment (e.g., `alteredPath/File`) |
| `output-directory` | No | `${{ github.event.repository.name }}` | Base directory in target repo |
| `commit-message-prefix` | No | `[actions] ` | Prefix for commit messages |
| `pr-branch-prefix` | No | `actions/${{ github.event.repository.name }}` | PR branch prefix |
| `pr-title` | No | `GitHub Actions: Update from ${{ github.event.repository.name }}` | PR title |
| `pr-message` | No | Automated update message | PR description |
| `git-name` | No | `github-actions[bot]` | Git committer name |
| `git-email` | No | `github-actions[bot]@users.noreply.github.com` | Git committer email |
| `git-token` | Yes | - | PAT with repo write access |

## Usage Examples

### Basic Usage
```yaml
- uses: ItsGiantCookie/smart-copy-cat@v1
  with:
    output-repo: 'owner/target-repo'
    output-branch: 'main'
    git-token: ${{ secrets.PAT_TOKEN }}
```
### With Path Transformation
```yaml
- uses: ItsGiantCookie/smart-copy-cat@v1
  with:
    output-repo: 'owner/target-repo'
    output-branch: 'main'
    path-pattern-to-prune: 'src/old-scenario/'
    path-pattern-to-insert: 'src/new-scenario/'
    git-token: ${{ secrets.PAT_TOKEN }}
```

### With Path Transformation
```yaml
- uses: ItsGiantCookie/smart-copy-cat@v1
  with:
    output-repo: 'owner/target-repo'
    output-branch: 'dev'
    output-directory: 'custom-directory'
    git-token: ${{ secrets.PAT_TOKEN }}
```
