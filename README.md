# gha-shared

Reusable GitHub Actions workflows shared across repos: commit-message linting and releases.

## commitlint

Caller repo, on pull requests:

```yaml
name: Lint Commit Messages

on:
  pull_request:

permissions:
  contents: read

jobs:
  commitlint:
    uses: jorkab/gha-shared/.github/workflows/commitlint.yml@v1
```

## release-it

Caller repo, on push to `main`:

```yaml
name: Release it

on:
  push:
    branches:
      - main

permissions:
  contents: write
  issues: write
  pull-requests: write

jobs:
  release:
    uses: jorkab/gha-shared/.github/workflows/release-it.yml@v1
    secrets:
      release-token: ${{ secrets.MY_RELEASE_PLEASE_TOKEN }}
```

Both workflows expect the caller repo to provide:

- `.nvmrc` (or pass `node-version-file` as a different path)
- a `release.config.js` (or pass `config-file` as a different path) exporting a commitlint config and a release-it config, e.g. via [`@jorkab/commit-conventions`](https://github.com/jorkab/commit-conventions)
- `npm ci` must succeed, i.e. `package-lock.json` committed and any private dependency reachable without extra auth

## Versioning

Pin callers to a tag (`@v1`), not `@main` — an unpinned reference means any future change to this repo silently changes behavior in every consuming repo.
