# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows that can be used across multiple repositories in the Nexus organization.

## Docker Build and Push Workflow

The `docker-publish.yml` workflow provides a standardized way to build and push Docker images to GitHub Container Registry (ghcr.io).

### Usage

Create a workflow file in your repository (e.g., `.github/workflows/docker.yml`):

```yaml
name: Build and Deploy Docker Image

on:
  push:
    branches: [main]
  pull_request:

jobs:
  docker:
    uses: Nexus-Group-Forge/nexus-common/.github/workflows/docker-publish.yml@main
    with:
      image-name: your-app-name
      dockerfile-path: Dockerfile
      build-context: .
    secrets: inherit
```

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `image-name` | string | ✅ | - | Name of the Docker image to build |
| `dockerfile-path` | string | ❌ | `Dockerfile` | Path to the Dockerfile (relative to repository root) |
| `build-context` | string | ❌ | `.` | Build context path |

### Behavior

- **Main branch pushes**: Creates both `latest` and SHA-tagged images
- **Other branch pushes**: Creates dev-prefixed SHA-tagged images (`dev-<sha>`)
- **Pull requests**: Builds and pushes dev-prefixed images

### Examples

#### Basic usage
```yaml
jobs:
  docker:
    uses: Nexus-Group-Forge/nexus-common/.github/workflows/docker-publish.yml@main
    with:
      image-name: my-api
    secrets: inherit
```

#### Custom Dockerfile location
```yaml
jobs:
  docker:
    uses: Nexus-Group-Forge/nexus-common/.github/workflows/docker-publish.yml@main
    with:
      image-name: my-frontend
      dockerfile-path: docker/Dockerfile.prod
      build-context: .
    secrets: inherit
```

### Required Permissions

Your calling workflow needs to include these permissions:

```yaml
permissions:
  contents: read
  packages: write
```

**Complete example with permissions:**
```yaml
name: Build and Deploy Docker Image

on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: read
  packages: write

jobs:
  docker:
    uses: Nexus-Group-Forge/nexus-common/.github/workflows/docker-publish.yml@main
    with:
      image-name: your-app-name
    secrets: inherit
```

**Why these permissions are needed:**
- `contents: read` - Required to checkout repository code
- `packages: write` - Required to push Docker images to GitHub Container Registry (ghcr.io)

**Alternative:** If your organization has restrictive default permissions, you may need to explicitly pass the GitHub token instead of using `secrets: inherit`.