# update

Opens pull requests for outdated dependencies. Runs `git-pkgs outdated` to find packages with newer versions available, then for each one creates a branch, runs `git-pkgs update <package>`, commits the lockfile changes, and opens a PR. Works across any ecosystem git-pkgs supports without per-manager configuration.

```yaml
- uses: git-pkgs/actions/setup@v1
- uses: git-pkgs/actions/update@v1
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `ecosystem` | _(empty)_ | Filter to a single ecosystem (`npm`, `cargo`, `bundler`, `gomod`, etc). When empty, all detected ecosystems are checked. |
| `limit` | `5` | Maximum number of new pull requests to open per run. Dependencies that already have an open PR don't count toward the limit. |
| `branch-prefix` | `git-pkgs/` | Prefix for branches the action creates. |
| `labels` | `dependencies` | Comma-separated labels applied to created pull requests. |
| `base` | default branch | Base branch for pull requests. |
| `token` | `github.token` | Token for pushing branches and opening PRs. Needs `contents: write` and `pull-requests: write`. |

## Outputs

| Name | Description |
|------|-------------|
| `outdated` | Total number of outdated dependencies found. |
| `opened` | Number of pull requests opened this run. |

## Scheduled workflow

Run on a schedule so updates appear regularly. The job needs write access to push branches and open PRs, and the relevant language toolchain so `git-pkgs update` can invoke the underlying package manager.

```yaml
name: Dependencies
on:
  schedule:
    - cron: "0 9 * * 1"
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - uses: git-pkgs/actions/setup@v1

      - uses: git-pkgs/actions/update@v1
        with:
          limit: 10
```

Swap the Ruby setup step for whatever your project uses (`actions/setup-node`, `actions/setup-go`, `dtolnay/rust-toolchain`, and so on). git-pkgs detects the package manager and shells out to it, so the toolchain must be on `PATH`.

## How PRs are managed

Each dependency gets its own branch named `<prefix><ecosystem>-<package>-<version>`. If a PR is already open on that branch the dependency is skipped, so reruns won't pile up duplicates. Close or merge a PR and the next run will recreate it if the package is still outdated.

The PR body includes the version bump and, when available, the changelog entries between the two versions fetched via `git-pkgs changelog`.

## Triggering CI on the PRs

GitHub doesn't run workflows on events caused by the default `GITHUB_TOKEN`. If you want CI to run on these PRs, use a personal access token or GitHub App token for both the checkout (so the push uses it) and this action (so the PR is opened with it):

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
    token: ${{ secrets.DEPS_TOKEN }}

- uses: git-pkgs/actions/update@v1
  with:
    token: ${{ secrets.DEPS_TOKEN }}
```
