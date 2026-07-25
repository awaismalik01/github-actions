# GitHub Actions

A collection of reusable composite GitHub Actions for CI/CD automation.

## Available Actions

| Action | Description |
|--------|-------------|
| [db-flyway](./db-flyway) | Run Flyway database migrations against PostgreSQL |
| [secure-flyway-migration](./secure-flyway-migration) | Fetch per-schema credentials from Infisical and run Flyway migrations |
| [nestjs-vercel-deploy](./nestjs-vercel-deploy) | Sync Infisical secrets, build, and deploy a Node.js/NestJS app to Vercel |
| [vercel-infisical-vault-sync](./vercel-infisical-vault-sync) | Fetch secrets from Infisical and sync them to a Vercel project |

## Usage

Reference any action in your workflow by path and git ref:

```yaml
- uses: awaismalik01/github-actions/<action-name>@v1
```

All Infisical-backed actions default to **OIDC** authentication. Ensure your workflow has `id-token: write` permission.

## Actions Overview

### db-flyway

Runs Flyway migrations on PostgreSQL. Downloads the Flyway CLI at runtime and executes migrations from a specified directory.

```yaml
- uses: awaismalik01/github-actions/db-flyway@v1
  with:
    db-url: "jdbc:postgresql://host:5432/mydb"
    db-user: ${{ secrets.DB_USER }}
    db-password: ${{ secrets.DB_PASSWORD }}
    migration-path: "./migrations"
    schema: "public"
```

### secure-flyway-migration

Wraps `db-flyway` with automatic credential resolution from Infisical. Expects `FLYWAY_USER`, `FLYWAY_PASSWORD`, and `DATABASE_URL` secrets at the specified path.

```yaml
- uses: awaismalik01/github-actions/secure-flyway-migration@v1
  with:
    infisical-identity-id: ${{ secrets.INFISICAL_MACHINE_ID }}
    infisical-project-slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
    infisical-env-slug: "dev"
    migration-path: "./db-migrations"
    schema: "auth_gateway"
```

### nestjs-vercel-deploy

Syncs Infisical secrets to Vercel, builds a Node.js application, and deploys it.

```yaml
- uses: awaismalik01/github-actions/nestjs-vercel-deploy@v1
  with:
    vercel_token: ${{ secrets.VERCEL_TOKEN }}
    vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
    vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
    target: "preview"
    infisical_identity_id: ${{ secrets.INFISICAL_MACHINE_ID }}
    infisical_project_slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
    infisical_env_slug: "staging"
```

### vercel-infisical-vault-sync

Fetches all secrets from an Infisical project/environment and upserts them as environment variables on a Vercel project.

```yaml
- uses: awaismalik01/github-actions/vercel-infisical-vault-sync@v1
  with:
    vercel-token: ${{ secrets.VERCEL_TOKEN }}
    vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
    target: "production"
    infisical-identity-id: ${{ secrets.INFISICAL_MACHINE_ID }}
    infisical-project-slug: "my-project"
    infisical-env-slug: "prod"
```

## License

Private repository.
