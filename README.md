# shared-github-actions

Reusable GitHub workflows shared across Albumo projects.

## Included workflows

- `.github/workflows/package-push.yml`
  - Builds and pushes Docker images to GHCR.
- `.github/workflows/release-package.yml`
  - Builds a versioned image and creates the immutable git release tag.
- `.github/workflows/validate-release-version.yml`
  - Reads and validates a release version, optionally requiring an existing git tag.
- `.github/workflows/run-migrations.yml`
  - Runs DB migrations from a shared migration path, optionally checking out a specific release version first.
- `.github/workflows/tag-git-ref.yml`
  - Creates immutable git tags and/or force-updates environment tags.
- `.github/workflows/tag-ghcr-image.yml`
  - Copies an existing GHCR image tag to one or more target tags.
- `.github/workflows/deploy-coolify.yml`
  - Triggers a Coolify deployment for a service tag.
- `.github/workflows/deploy-release.yml`
  - Advances mutable environment git/image tags and triggers Coolify.

## Usage

Reference workflows from other repositories:

```yaml
jobs:
  package:
    uses: bjbeecher/shared-github-actions/.github/workflows/package-push.yml@v1
    with:
      dockerfile: api/docker/Dockerfile
      service: api
      version: v1.2.3
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
      KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
```

When `version` is provided, the workflow pushes three tags for the image:

- `latest`
- `${{ github.sha }}`
- the explicit `version` value

```yaml
jobs:
  validate:
    uses: bjbeecher/shared-github-actions/.github/workflows/validate-release-version.yml@v1
```

```yaml
jobs:
  release:
    uses: bjbeecher/shared-github-actions/.github/workflows/release-package.yml@v1
    with:
      dockerfile: api/docker/Dockerfile
      service: api
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
      KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
```

```yaml
jobs:
  migrate:
    uses: bjbeecher/shared-github-actions/.github/workflows/run-migrations.yml@v1
    with:
      version: v1.2.3
    secrets:
      DATABASE_URL: ${{ secrets.POSTGRES_URL }}
```

```yaml
jobs:
  deploy:
    uses: bjbeecher/shared-github-actions/.github/workflows/deploy-release.yml@v1
    with:
      service: api
      version: v1.2.3
      environment_tag: development
      coolify_tag: development-albumo-api
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
      COOLIFY_URL: ${{ secrets.COOLIFY_URL }}
      COOLIFY_API_TOKEN: ${{ secrets.COOLIFY_API_TOKEN }}
```

Omit `version` to have the workflow read `VERSION`. For production-style promotion where the environment tag must move to a specific release tag, also pass `git_source_ref: refs/tags/v1.2.3`.

## Publishing checklist

1. Create a new repository named `shared-github-actions`.
2. Push this folder's contents to that repository.
3. Create a tag (for example `v1`) after validating workflows.
4. Update caller repos to pin to immutable tags.
