# .github

Shared reusable GitHub Actions workflows for `cwaits6` repos.

## Available Workflows

| Workflow | Description | Inputs |
|----------|-------------|--------|
| `dependency-review.yml` | Dependency change review on PRs | `fail-on-severity` (default: `high`) |
| `trivy.yml` | Trivy filesystem vulnerability scan | `severity` (default: `HIGH,CRITICAL`), `scan-type` |
| `release.yml` | Semantic-release with SBOM generation | `node-version`, `generate-sbom` |
| `cleanup-preview.yml` | Clean up Vercel preview deployments on PR close | `production-keep-count`; secrets: `VERCEL_PROJECT_ID`, `VERCEL_TOKEN` |
| `semgrep.yml` | Semgrep scan with autofix, PR comments, main branch issues | `semgrep-config` (default: `auto`) |

## Usage

In your repo, create thin caller workflows that reference these:

```yaml
# .github/workflows/dependency-review.yml
name: Dependency Review
on:
  pull_request:
    branches: [main]
jobs:
  review:
    uses: cwaits6/.github/.github/workflows/dependency-review.yml@main
```

```yaml
# .github/workflows/trivy.yml
name: Trivy Security Scan
on:
  pull_request:
    branches: [main]
jobs:
  scan:
    uses: cwaits6/.github/.github/workflows/trivy.yml@main
```

```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    branches: [main]
jobs:
  release:
    uses: cwaits6/.github/.github/workflows/release.yml@main
```

```yaml
# .github/workflows/semgrep.yml
name: Semgrep
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
jobs:
  scan:
    uses: cwaits6/.github/.github/workflows/semgrep.yml@main
```

```yaml
# .github/workflows/cleanup-preview.yml
name: Cleanup Deployments
on:
  pull_request:
    types: [closed]
jobs:
  cleanup:
    uses: cwaits6/.github/.github/workflows/cleanup-preview.yml@main
    secrets:
      VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
      VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}
```

## Overriding Defaults

Pass inputs to customize behavior:

```yaml
jobs:
  scan:
    uses: cwaits6/.github/.github/workflows/trivy.yml@main
    with:
      severity: "CRITICAL"
```

## Testing Changes

1. Push changes to a feature branch in this repo
2. Point a consuming repo at the branch: `uses: cwaits6/.github/.github/workflows/trivy.yml@my-branch`
3. Open a PR in the consuming repo to trigger the workflow
4. Once validated, merge here, then revert the consuming repo back to `@main`
