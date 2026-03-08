# sbom

Generates a Software Bill of Materials in [CycloneDX](https://cyclonedx.org/) or [SPDX](https://spdx.dev/) format. By default the SBOM is uploaded as a workflow artifact.

```yaml
- uses: git-pkgs/actions/setup@v1
- uses: git-pkgs/actions/sbom@v1
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `format` | `cyclonedx` | SBOM format: `cyclonedx` or `spdx`. |
| `name` | Repository name | Component name for the SBOM. |
| `output` | `sbom.json` | Output file path. |
| `upload` | `true` | Upload the SBOM as a workflow artifact. Set to `false` to skip. |

## Outputs

| Name | Description |
|------|-------------|
| `file` | Path to the generated SBOM file. |

## Attach to a release

Generate an SBOM and attach it to a GitHub release instead of uploading as an artifact:

```yaml
name: Release
on:
  release:
    types: [published]

permissions:
  contents: write

jobs:
  sbom:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: git-pkgs/actions/setup@v1

      - uses: git-pkgs/actions/sbom@v1
        with:
          upload: "false"

      - name: Attach to release
        env:
          GH_TOKEN: ${{ github.token }}
        run: gh release upload ${{ github.event.release.tag_name }} sbom.json
```

## SPDX format

```yaml
- uses: git-pkgs/actions/sbom@v1
  with:
    format: "spdx"
```

## Custom output path

```yaml
- uses: git-pkgs/actions/sbom@v1
  with:
    output: "build/sbom.cdx.json"
```
