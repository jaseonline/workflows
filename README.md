# workflows

Reusable GitHub Actions workflows shared by jaseonline repos. Each repo keeps a short caller that owns the triggers, permissions and secrets; the logic lives here.

| Workflow | Caller triggers | Inputs | Secrets |
| --- | --- | --- | --- |
| `lint.yml` | `push`, `pull_request`, `workflow_dispatch` | `filter-regex-exclude`, `linter-rules-path` | none |
| `claude.yml` | `issue_comment`, `pull_request_review_comment`, `issues`, `pull_request_review` | none | `CLAUDE_CODE_OAUTH_TOKEN` |
| `claude-code-review.yml` | `pull_request` | none | `CLAUDE_CODE_OAUTH_TOKEN` |
| `backup.yml` | `schedule`, `workflow_dispatch` | `bucket`, `retention-days` | `CF_ACCOUNT_ID`, `R2_ACCESS_KEY_ID`, `R2_SECRET_ACCESS_KEY` |
| `readme-date.yml` | `push`, `workflow_dispatch` | `readme-path` | none |

## Calling one

```yaml
jobs:
  lint:
    permissions:
      contents: read
      packages: read
      statuses: write
    uses: jaseonline/workflows/.github/workflows/lint.yml@<sha> # v1.0.0
```

- **Permissions:** the caller's job permissions are the ceiling; a reusable workflow can't raise them. Grant what each workflow's job lists.
- **Secrets:** pass them by name, not `secrets: inherit` (zizmor flags it, and each workflow gets only what it needs).
- **Pinning:** full commit SHA with the tag as a comment. Dependabot bumps callers when a new tag is released.

[jaseonline/repo-template](https://github.com/jaseonline/repo-template) ships working callers for all five.

## Releasing

Changes reach other repos only when they merge the Dependabot bump, so tag every release:

```sh
git tag v1.1.0 && git push origin v1.1.0 && gh release create v1.1.0 --generate-notes
```

Breaking input or secret changes need a major version.
