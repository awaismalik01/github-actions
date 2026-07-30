# react-vercel-deploy

A reusable GitHub Action that syncs Infisical secrets to a Vercel project and deploys a React/Vite application. Unlike `nestjs-vercel-deploy`, this action does not run a local build step — Vercel handles the build using the project's framework detection (Vite, CRA, Next.js static, etc.).

## Features

- Syncs environment variables from Infisical to Vercel (via `vercel-infisical-vault-sync`)
- Deploys to Vercel with Vercel-managed build (no local Node.js setup or `npm run build`)
- Supports preview and production targets

## Usage

### With OIDC Auth (default)

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Deploy to Vercel
    uses: awaismalik01/github-actions/react-vercel-deploy@v1
    with:
      vercel_token: ${{ secrets.VERCEL_TOKEN }}
      vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
      vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "preview"
      infisical_identity_id: ${{ secrets.INFISICAL_MACHINE_ID }}
      infisical_project_slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
      infisical_env_slug: "dev"
```

### With Universal Auth

```yaml
permissions:
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Deploy to Vercel
    uses: awaismalik01/github-actions/react-vercel-deploy@v1
    with:
      vercel_token: ${{ secrets.VERCEL_TOKEN }}
      vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
      vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "production"
      infisical_method: "universal"
      infisical_client_id: ${{ secrets.INFISICAL_CLIENT_ID }}
      infisical_client_secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
      infisical_project_slug: "my-project"
      infisical_env_slug: "prod"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `vercel_token` | Vercel authentication token | Yes | — |
| `vercel_org_id` | Vercel org/team ID | Yes | — |
| `vercel_project_id` | Vercel project ID | Yes | — |
| `target` | Deployment target (`preview`, `production`, `development`) | Yes | — |
| `infisical_method` | Auth method (`oidc`, `universal`, `aws-iam`) | No | `oidc` |
| `infisical_identity_id` | Machine Identity ID (for oidc/aws-iam) | No | — |
| `infisical_client_id` | Machine Identity client ID (for universal) | No | — |
| `infisical_client_secret` | Machine Identity client secret (for universal) | No | — |
| `infisical_domain` | Infisical instance URL | No | `https://app.infisical.com` |
| `infisical_project_slug` | Infisical project slug | Yes | — |
| `infisical_env_slug` | Infisical environment slug (e.g. `dev`, `staging`, `prod`) | Yes | — |
| `infisical_secret_path` | Infisical secret path | No | `/` |

## How It Works

1. Syncs secrets from Infisical to the Vercel project (via `vercel-infisical-vault-sync`).
2. Installs the latest Vercel CLI globally.
3. Pulls Vercel project settings for the specified environment.
4. Runs `vercel build` which uses the project's framework settings (Vite, etc.) to build the app remotely.
5. Deploys the prebuilt artifacts to Vercel.

## Notes

- OIDC auth requires `id-token: write` permission in the calling workflow.
- The Infisical secret sync happens before the build, so env vars (e.g. `VITE_APP_API_URL`) are available at build time.
- No local `npm install` or `npm run build` is needed — Vercel's build system handles dependency installation and compilation.
