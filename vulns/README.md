# vulns

Scans dependencies for known vulnerabilities using the [OSV database](https://osv.dev). Can report findings, block the build on a severity threshold, and upload results to GitHub Advanced Security via SARIF.

```yaml
- uses: git-pkgs/actions/setup@v1
- uses: git-pkgs/actions/vulns@v1
  with:
    severity: "high"
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `severity` | _(empty)_ | Minimum severity to fail on: `critical`, `high`, `medium`, `low`. When empty, the action reports findings but doesn't fail. |
| `sarif` | `false` | Upload results as SARIF to GitHub Advanced Security. |
| `token` | `github.token` | GitHub token for SARIF upload. Needs `security-events: write` permission. |

## Outputs

| Name | Description |
|------|-------------|
| `found` | `true` if any vulnerabilities were found, `false` otherwise. |
| `count` | Number of vulnerabilities found. |

## Report only

Without a `severity` input the action prints vulnerabilities but always passes. Useful for visibility without blocking merges:

```yaml
- uses: git-pkgs/actions/vulns@v1
```

## Severity gate

Set `severity` to fail the build when vulnerabilities at or above that level exist. The threshold is inclusive downward, so `high` fails on both `high` and `critical`:

```yaml
- uses: git-pkgs/actions/vulns@v1
  with:
    severity: "high"
```

## SARIF upload

Upload results to GitHub's Security tab so vulnerabilities appear alongside code scanning alerts:

```yaml
permissions:
  contents: read
  security-events: write

steps:
  - uses: git-pkgs/actions/setup@v1
  - uses: git-pkgs/actions/vulns@v1
    with:
      sarif: "true"
```

## Using outputs

The `found` and `count` outputs let you branch on results in later steps:

```yaml
- uses: git-pkgs/actions/vulns@v1
  id: vulns

- if: steps.vulns.outputs.found == 'true'
  run: echo "Found ${{ steps.vulns.outputs.count }} vulnerabilities"
```
