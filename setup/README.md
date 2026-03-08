# setup

Installs [git-pkgs](https://github.com/git-pkgs/git-pkgs) and initializes the dependency database. Run this before any other git-pkgs action.

Your checkout step needs `fetch-depth: 0` so git-pkgs can walk the full commit history.

```yaml
steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 0

  - uses: git-pkgs/actions/setup@v1
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `version` | `latest` | git-pkgs version to install (e.g. `0.1.9`). Resolves the latest release when omitted. |
| `reindex` | `true` | Run `git-pkgs reindex` after init to pick up new commits. Set to `false` to skip. |

## What it does

1. Downloads the git-pkgs binary for the runner's platform from GitHub releases
2. Installs it to `/usr/local/bin`
3. Runs `git-pkgs init` to build the SQLite database (skipped if one already exists)
4. Runs `git-pkgs reindex` to pick up any commits since the last index

## Pinning a version

If you want reproducible builds, pin to a specific version rather than using `latest`:

```yaml
- uses: git-pkgs/actions/setup@v1
  with:
    version: "0.1.9"
```
