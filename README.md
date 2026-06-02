# `.github` — Scott-Builds-AI org defaults

Org-wide GitHub configuration for [`Scott-Builds-AI`](https://github.com/Scott-Builds-AI).

## What lives here

| File | Purpose |
|---|---|
| [`.github/workflows/ci.yml`](./.github/workflows/ci.yml) | Reusable CI for **package repos** (hub, hub-sdk, hub-ui, tool-manifest). Lint / typecheck / test / build. |
| [`.github/workflows/tool-ci.yml`](./.github/workflows/tool-ci.yml) | Reusable CI for **tool repos**. Same shape as `ci.yml` plus the Phase 9 `okai-scan` contract check. |
| [`.github/workflows/tool-deploy.yml`](./.github/workflows/tool-deploy.yml) | Reusable deploy for **edge-tier tool repos**. OpenNext build + Wrangler deploy on push to main. |

## Usage from a consumer repo

### Package repos — `ci.yml`

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
      package-manager: "pnpm"
      run-test: true
```

### Tool repos — `tool-ci.yml` + `tool-deploy.yml`

```yaml
# <tool-repo>/.github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  ci:
    uses: Scott-Builds-AI/.github/.github/workflows/tool-ci.yml@main
```

```yaml
# <tool-repo>/.github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: Scott-Builds-AI/.github/.github/workflows/tool-deploy.yml@main
    secrets:
      CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
      CLOUDFLARE_ACCOUNT_ID: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
```

### Required secrets for deploy

The Cloudflare credentials need to be available to every tool repo's deploy. Two options:

1. **Per-repo** — add to `<tool-repo>/Settings/Secrets and variables/Actions`.
2. **Org-wide** — easier — set once at https://github.com/organizations/Scott-Builds-AI/settings/secrets/actions with **"All repositories"** visibility:

```bash
gh secret set CLOUDFLARE_API_TOKEN --org Scott-Builds-AI --visibility all --body "<token>"
gh secret set CLOUDFLARE_ACCOUNT_ID --org Scott-Builds-AI --visibility all --body "<account-id>"
```

The API token needs **Workers Edit** + **Account Read** + **User Read** + **D1 / R2 / KV** depending on what your tools touch. Easiest: clone the **"Edit Cloudflare Workers"** template at https://dash.cloudflare.com/profile/api-tokens.

## Context

This repo is part of the OpenKey AI polyrepo. Architecture, security, and conventions all live in [`hub/docs/`](https://github.com/Scott-Builds-AI/hub/tree/main/docs).
