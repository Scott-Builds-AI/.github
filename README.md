# `.github` — Scott-Builds-AI org defaults

Org-wide GitHub configuration for [`Scott-Builds-AI`](https://github.com/Scott-Builds-AI).

## What lives here

| File | Purpose |
|---|---|
| [`.github/workflows/ci.yml`](./.github/workflows/ci.yml) | Reusable CI workflow (lint / typecheck / test / build). Every other repo references this via `workflow_call`. |

## Usage from a consumer repo

```yaml
# <consumer-repo>/.github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  ci:
    uses: Scott-Builds-AI/.github/.github/workflows/ci.yml@main
    with:
      package-manager: "pnpm"      # or "npm"
      run-test: true               # or false during the bootstrapping phase
```

## Context

This repo is part of the OpenKey AI polyrepo. Architecture, security, and conventions all live in [`hub/docs/`](https://github.com/Scott-Builds-AI/hub/tree/main/docs).
