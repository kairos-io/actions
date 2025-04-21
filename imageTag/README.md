# Generate Image Tag Action

This GitHub Action generates an image tag based on the provided inputs. It is designed to dynamically construct image tags for container images, incorporating details such as the base image, variant, architecture, model, Kubernetes distribution, and more.

## Inputs

| Name                | Description                                           | Required | Default   |
|---------------------|-------------------------------------------------------|----------|-----------|
| `base_image`        | The base image name.                                  | Yes      |           |
| `variant`           | The variant of the image.                             | Yes      |           |
| `arch`              | The architecture of the image.                        | Yes      |           |
| `model`             | The model of the image.                               | No       | `generic` |
| `kubernetes_distro` | The Kubernetes distribution.                          | No       |           |
| `trusted_boot`      | Whether trusted boot is enabled (`true`/`false`).     | No       | `false`   |
| `event_type`        | The GitHub event type (e.g., `push`, `pull_request`). | Yes      |           |
| `event_ref`         | The Git reference (e.g., branch name, tag).           | Yes      |           |

## Outputs

| Name        | Description              |
|-------------|--------------------------|
| `image_tag` | The generated image tag. |

## Usage

Below is an example of how to use this action in a workflow:

```yaml
name: Build Image Tag

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Generate Image Tag
        id: generate_tag
        uses: ./imageTag
        with:
          base_image: 'ubuntu:22.04'
          variant: 'core'
          arch: 'amd64'
          model: 'generic'
          kubernetes_distro: 'k3s'
          trusted_boot: 'true'
          event_type: ${{ github.event_name }}
          event_ref: ${{ github.ref_name }}

      - name: Output Image Tag
        run: |
          echo "Generated Image Tag: ${{ steps.generate_tag.outputs.image_tag }}"
```


## How It Works
- **Base Image Parsing**: The action extracts the base image name and tag.
- **Dynamic Tag Construction**: Combines inputs like variant, arch, model, and kubernetes_distro to form the image tag.
- **Event Context**: Uses event_type and event_ref to append the Git reference or commit SHA to the tag.
- **Trusted Boot**: Adds -uki to the tag if trusted_boot is set to true.
