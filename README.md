# git-pkgs actions

Reusable GitHub Actions for [git-pkgs](https://github.com/git-pkgs/git-pkgs) dependency analysis.

## Actions

### `git-pkgs/actions/setup`

Installs git-pkgs and initializes the dependency database.

```yaml
- uses: git-pkgs/actions/setup@v1
  with:
    version: "0.19.0" # optional, defaults to latest
```

### `git-pkgs/actions/diff`

Comments on PRs with a summary of dependency changes (added, removed, updated).

```yaml
- uses: git-pkgs/actions/diff@v1
```

### `git-pkgs/actions/vulns`

Scans for known vulnerabilities. Can block on severity threshold and upload SARIF to GitHub Advanced Security.

```yaml
- uses: git-pkgs/actions/vulns@v1
  with:
    severity: "high" # fail on high or critical
    sarif: "true"    # upload to GitHub Advanced Security
```

### `git-pkgs/actions/licenses`

Enforces license policy with allow/deny lists.

```yaml
- uses: git-pkgs/actions/licenses@v1
  with:
    allow: "MIT,Apache-2.0,BSD-2-Clause,BSD-3-Clause,ISC"
```

### `git-pkgs/actions/sbom`

Generates a CycloneDX or SPDX Software Bill of Materials and uploads it as a workflow artifact.

```yaml
- uses: git-pkgs/actions/sbom@v1
  with:
    format: "cyclonedx" # or spdx
```

### `git-pkgs/actions/update`

Opens pull requests for outdated dependencies, one PR per package. Run on a schedule for a lightweight Dependabot replacement.

```yaml
- uses: git-pkgs/actions/update@v1
  with:
    limit: 10
```

## Full example

```yaml
name: Dependencies
on:
  pull_request:

permissions:
  contents: read
  pull-requests: write
  security-events: write

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: git-pkgs/actions/setup@v1

      - uses: git-pkgs/actions/diff@v1

      - uses: git-pkgs/actions/vulns@v1
        with:
          severity: "high"

      - uses: git-pkgs/actions/licenses@v1
        with:
          deny: "GPL-3.0-only,AGPL-3.0-only"
```

Note: `fetch-depth: 0` is required so git-pkgs can access the full commit history.

## License

Copyright (c) 2026 Andrew Nesbitt. MIT License.
