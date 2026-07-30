# vercel-infisical-vault-sync

A reusable GitHub Action that fetches environment variables from [Infisical](https://infisical.com) and syncs them to a [Vercel](https://vercel.com) project. All secret values are masked in workflow logs.

## How It Works

1. Authenticates with Infisical using Machine Identity (OIDC, universal, or AWS IAM).
2. Fetches all secrets at the specified project/environment/path.
3. Iterates over each secret and upserts it to the Vercel project via the Vercel API.

This removes the need to manually configure Infisical integrations in the Vercel dashboard — secrets flow automatically as part of your CI/CD pipeline.

## Usage

### With OIDC Auth (default)

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - name: Sync Infisical secrets to Vercel
    uses: awaismalik01/github-actions/vercel-infisical-vault-sync@v1
    with:
      vercel-token: ${{ secrets.VERCEL_TOKEN }}
      vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "preview"
      infisical-identity-id: ${{ secrets.INFISICAL_MACHINE_ID }}
      infisical-project-slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
      infisical-env-slug: "staging"
```

### With Universal Auth

```yaml
permissions:
  contents: read

steps:
  - name: Sync Infisical secrets to Vercel
    uses: awaismalik01/github-actions/vercel-infisical-vault-sync@v1
    with:
      vercel-token: ${{ secrets.VERCEL_TOKEN }}
      vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "production"
      infisical-method: "universal"
      infisical-client-id: ${{ secrets.INFISICAL_CLIENT_ID }}
      infisical-client-secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
      infisical-project-slug: "my-project"
      infisical-env-slug: "prod"
```

### With Team-Owned Project

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - name: Sync Infisical secrets to Vercel
    uses: awaismalik01/github-actions/vercel-infisical-vault-sync@v1
    with:
      vercel-token: ${{ secrets.VERCEL_TOKEN }}
      vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
      vercel-team-id: ${{ secrets.VERCEL_TEAM_ID }}
      target: "production"
      infisical-identity-id: ${{ secrets.INFISICAL_MACHINE_ID }}
      infisical-project-slug: "my-project"
      infisical-env-slug: "prod"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `vercel-token` | Vercel API token | Yes | — |
| `vercel-project-id` | Vercel Project ID | Yes | — |
| `vercel-team-id` | Vercel Team ID (for team-owned projects) | No | — |
| `target` | Vercel environment target (`development`, `preview`, `production`) | No | `preview` |
| `infisical-method` | Auth method (`oidc`, `universal`, `aws-iam`) | No | `oidc` |
| `infisical-identity-id` | Machine Identity ID (for oidc/aws-iam) | No | — |
| `infisical-client-id` | Machine Identity client ID (for universal) | No | — |
| `infisical-client-secret` | Machine Identity client secret (for universal) | No | — |
| `infisical-domain` | Infisical instance URL | No | `https://app.infisical.com` |
| `infisical-project-slug` | Infisical project slug | Yes | — |
| `infisical-env-slug` | Infisical environment slug (e.g. `dev`, `staging`, `prod`) | Yes | — |
| `infisical-secret-path` | Infisical secret path | No | `/` |

## Notes

- OIDC auth requires `id-token: write` permission in the calling workflow.
- The action uses `upsert=true` on the Vercel API — existing variables with the same key are updated, new ones are created.
- All secret values are masked using `::add-mask::` before being sent to Vercel.
- If no secrets are found at the specified Infisical path, the action emits a warning and exits successfully.
- The action requires `jq` and `curl` on the runner (available by default on `ubuntu-latest`).
