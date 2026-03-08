# diff

Posts a comment on pull requests summarizing dependency changes between the base branch and the PR head. If the PR is updated, the existing comment is edited rather than creating a new one.

```yaml
- uses: git-pkgs/actions/setup@v1
- uses: git-pkgs/actions/diff@v1
```

## Inputs

| Name | Default | Description |
|------|---------|-------------|
| `base` | PR base SHA | Base ref to compare from. Defaults to the pull request's base commit. |
| `head` | `HEAD` | Head ref to compare to. |
| `token` | `github.token` | GitHub token used for commenting. Needs `pull-requests: write` permission. |

## Permissions

The workflow needs `pull-requests: write` so the action can post and update comments:

```yaml
permissions:
  contents: read
  pull-requests: write
```

## How the comment works

The action runs `git-pkgs diff <base>..<head>` and posts the output as a PR comment. When dependencies haven't changed, no comment is created.

On subsequent pushes to the same PR, the action finds its previous comment (matched by the `### Dependency Changes` heading) and updates it in place. This keeps the PR timeline clean instead of accumulating stale comments.

## Custom base ref

To compare against something other than the PR base (say, a release tag):

```yaml
- uses: git-pkgs/actions/diff@v1
  with:
    base: "v1.0.0"
```
