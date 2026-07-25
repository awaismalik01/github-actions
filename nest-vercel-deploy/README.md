# nest-vercel-deploy

A reusable GitHub Action that syncs Infisical secrets to a Vercel project, builds a Node.js/NestJS application, and deploys it to Vercel.

## Features

- Syncs environment variables from Infisical to Vercel (via `vercel-infisical-vault-sync`)
- Sets up Node.js 24
- Installs project dependencies and builds the application
- Deploys to Vercel (supports preview and production targets)

## Usage

### With OIDC Auth (default)

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Deploy to Vercel
    uses: awaismalik01/github-actions/nest-vercel-deploy@v1
    with:
      vercel_token: ${{ secrets.VERCEL_TOKEN }}
      vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
      vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "preview"
      infisical_identity_id: ${{ secrets.INFISICAL_MACHINE_ID }}
      infisical_project_slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
      infisical_env_slug: "staging"
```

### With Universal Auth

```yaml
permissions:
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Deploy to Vercel
    uses: awaismalik01/github-actions/nest-vercel-deploy@v1
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

### Custom Build Commands

```yaml
- name: Deploy with custom commands
  uses: awaismalik01/github-actions/nest-vercel-deploy@v1
  with:
    vercel_token: ${{ secrets.VERCEL_TOKEN }}
    vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
    vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
    target: "production"
    install_command: "yarn install --frozen-lockfile"
    build_command: "yarn build"
    infisical_identity_id: ${{ secrets.INFISICAL_MACHINE_ID }}
    infisical_project_slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
    infisical_env_slug: "prod"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `vercel_token` | Vercel authentication token | Yes | — |
| `vercel_org_id` | Vercel org/team ID | Yes | — |
| `vercel_project_id` | Vercel project ID | Yes | — |
| `target` | Deployment target (`preview`, `production`, `development`) | Yes | — |
| `install_command` | Command to install dependencies | No | `npm install` |
| `build_command` | Command to build the application | No | `npm run build` |
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
2. Sets up Node.js 24 using `actions/setup-node@v7.0.0`.
3. Installs the latest Vercel CLI globally.
4. Runs the install command to fetch project dependencies.
5. Runs the build command to compile the application.
6. Pulls Vercel project settings for the specified environment.
7. Builds Vercel deployment artifacts (with `--prod` flag for production).
8. Deploys the prebuilt artifacts to Vercel.

## Notes

- OIDC auth requires `id-token: write` permission in the calling workflow.
- The Infisical secret sync happens before the build, so env vars are available on the Vercel project at deploy time.
