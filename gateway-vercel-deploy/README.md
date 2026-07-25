# gateway-vercel-deploy

A reusable GitHub Action that resolves environment variable placeholders in `vercel.json`, syncs Infisical secrets to a Vercel project, and deploys a Vercel edge gateway (no application build step).

## Features

- Fetches secrets from Infisical once and uses them for both placeholder resolution and Vercel env sync
- Automatically resolves all `${VAR}` placeholders in `vercel.json` that match keys from Infisical — no manual list needed
- Syncs runtime environment variables to Vercel via the Vercel API
- Sets up Node.js 24 and installs project dependencies (edge-runtime packages)
- Deploys to Vercel using prebuilt artifacts (supports preview and production targets)

## Usage

### With OIDC Auth (default)

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Deploy Gateway to Vercel
    uses: awaismalik01/github-actions/gateway-vercel-deploy@v1
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

  - name: Deploy Gateway to Vercel
    uses: awaismalik01/github-actions/gateway-vercel-deploy@v1
    with:
      vercel_token: ${{ secrets.VERCEL_TOKEN }}
      vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
      vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "production"
      infisical_method: "universal"
      infisical_client_id: ${{ secrets.INFISICAL_CLIENT_ID }}
      infisical_client_secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
      infisical_project_slug: "my-gateway"
      infisical_env_slug: "prod"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `vercel_token` | Vercel authentication token | Yes | — |
| `vercel_org_id` | Vercel org/team ID | Yes | — |
| `vercel_project_id` | Vercel project ID | Yes | — |
| `target` | Deployment target (`preview`, `production`) | Yes | — |
| `vercel_json_path` | Path to the vercel.json file to resolve placeholders in | No | `vercel.json` |
| `infisical_method` | Auth method (`oidc`, `universal`, `aws-iam`) | No | `oidc` |
| `infisical_identity_id` | Machine Identity ID (for oidc/aws-iam) | No | — |
| `infisical_client_id` | Machine Identity client ID (for universal) | No | — |
| `infisical_client_secret` | Machine Identity client secret (for universal) | No | — |
| `infisical_domain` | Infisical instance URL | No | `https://app.infisical.com` |
| `infisical_project_slug` | Infisical project slug | Yes | — |
| `infisical_env_slug` | Infisical environment slug (e.g. `dev`, `staging`, `prod`) | Yes | — |
| `infisical_secret_path` | Infisical secret path | No | `/` |

## How It Works

1. Fetches secrets from Infisical once (exported to a file).
2. Reads the keys from the secrets file and builds a substitution list automatically.
3. Sources the secrets and resolves matching `${VAR}` placeholders in `vercel.json` via `envsubst`.
4. Syncs all secrets to the Vercel project as runtime environment variables via the Vercel API.
5. Sets up Node.js 24 using `actions/setup-node@v7.0.0`.
6. Installs the latest Vercel CLI globally.
7. Installs project dependencies (e.g. `jose`, `@vercel/functions`).
8. Pulls Vercel project settings for the specified environment.
9. Builds Vercel deployment artifacts (edge functions, middleware).
10. Deploys the prebuilt artifacts to Vercel.

## Notes

- OIDC auth requires `id-token: write` permission in the calling workflow.
- Placeholder resolution is automatic — any key present in Infisical that appears as `${KEY}` in `vercel.json` will be resolved. No manual list to maintain.
- Unlike `nestjs-vercel-deploy`, this action has no application build step — Vercel bundles the edge middleware directly during `vercel build`.
- Infisical is called exactly once. The same secrets file is used for both placeholder resolution and Vercel env sync.
