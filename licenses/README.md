# licenses

Enforces a license policy on your dependencies. Provide an allow list, a deny list, or both. The build fails if any dependency violates the policy.

```yaml
- uses: git-pkgs/actions/setup@v1
- uses: git-pkgs/actions/licenses@v1
  with:
    allow: "MIT,Apache-2.0,BSD-2-Clause,BSD-3-Clause,ISC"
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `allow` | _(empty)_ | Comma-separated list of allowed SPDX license identifiers. Dependencies with any other license will fail the check. |
| `deny` | _(empty)_ | Comma-separated list of denied SPDX license identifiers. Dependencies with a matching license will fail the check. |

At least one of `allow` or `deny` must be set.

## Outputs

| Name | Description |
|------|-------------|
| `violations` | `true` if any violations were found, `false` otherwise. |

## Allow list vs deny list

An allow list is stricter: only the listed licenses are permitted, everything else fails. Good when you know exactly which licenses your legal team has approved.

```yaml
- uses: git-pkgs/actions/licenses@v1
  with:
    allow: "MIT,Apache-2.0,BSD-2-Clause,BSD-3-Clause,ISC,Unlicense"
```

A deny list is more permissive: everything is allowed except the listed licenses. Good for blocking specific problematic licenses while accepting the rest.

```yaml
- uses: git-pkgs/actions/licenses@v1
  with:
    deny: "GPL-3.0-only,AGPL-3.0-only,SSPL-1.0"
```

You can combine both if needed.

## License identifiers

Use [SPDX license identifiers](https://spdx.org/licenses/). Common ones:

- `MIT` - MIT License
- `Apache-2.0` - Apache License 2.0
- `BSD-2-Clause` - BSD 2-Clause
- `BSD-3-Clause` - BSD 3-Clause
- `ISC` - ISC License
- `GPL-2.0-only` - GNU GPL v2
- `GPL-3.0-only` - GNU GPL v3
- `AGPL-3.0-only` - GNU AGPL v3
- `LGPL-2.1-only` - GNU LGPL v2.1
- `MPL-2.0` - Mozilla Public License 2.0
