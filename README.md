# shared-actions

Reusable GitHub workflows shared across Albumo projects.

## Included workflows

- `.github/workflows/package-push.yml`
  - Builds and pushes Docker images to GHCR.
- `.github/workflows/deploy-coolify.yml`
  - Triggers a Coolify deployment for a service tag.

## Usage

Reference workflows from other repositories:

```yaml
jobs:
  package:
    uses: bjbeecher/shared-actions/.github/workflows/package-push.yml@v1
    with:
      dockerfile: api/docker/Dockerfile
      service: api
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
      KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
```

```yaml
jobs:
  deploy:
    uses: bjbeecher/shared-actions/.github/workflows/deploy-coolify.yml@v1
    with:
      tag: production-albumo-api
    secrets:
      COOLIFY_URL: ${{ secrets.COOLIFY_URL }}
      COOLIFY_API_TOKEN: ${{ secrets.COOLIFY_API_TOKEN }}
```

## Publishing checklist

1. Create a new repository named `shared-actions`.
2. Push this folder's contents to that repository.
3. Create a tag (for example `v1`) after validating workflows.
4. Update caller repos to pin to immutable tags.
# shared-github-actions
