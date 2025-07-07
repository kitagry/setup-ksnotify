# Setup ksnotify Action

This GitHub Action sets up [ksnotify](https://github.com/hirosassa/ksnotify) and makes it available in your workflow to notify Kubernetes changes as GitHub PR comments.

## Overview

ksnotify is a CLI tool that parses `kubectl diff` results and notifies changes to GitLab or GitHub. This action allows you to visualize Kubernetes resource changes in your CI/CD pipeline.

## Usage

### Basic Example

```yaml
name: Deploy and Notify

on:
  pull_request:
    paths:
      - 'k8s/**'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup ksnotify
        uses: kitagry/setup-ksnotify@v1

      - name: Setup kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: 'latest'

      - name: Diff and notify
        run: |
          kubectl diff -f k8s/ 2> /dev/null | ksnotify --ci github
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### With Skaffold

```yaml
      - name: Render and diff with Skaffold
        run: |
          skaffold render -p dev | kubectl diff -f - 2> /dev/null | ksnotify --ci github --suppress-skaffold
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Update Existing Comment

```yaml
      - name: Diff and update comment
        run: |
          kubectl diff -f k8s/ 2> /dev/null | ksnotify --ci github --patch
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

This action currently takes no inputs. It installs the latest version of ksnotify and adds it to PATH.

## Requirements

- `GITHUB_TOKEN` is required (automatically available in GitHub Actions)
- kubectl must be set up separately
- Kubernetes cluster connection must be configured beforehand

## License

This action is provided under the MIT License.
