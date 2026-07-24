# GitHub Actions

A collection of reusable composite GitHub Actions for CI/CD automation.

## Available Actions

| Action | Description |
|--------|-------------|
| [db-flyway](./db-flyway) | Run Flyway database migrations against PostgreSQL |
| [secure-flyway-migration](./secure-flyway-migration) | Fetch per-schema credentials from Infisical and run Flyway migrations |
| [nestjs-vercel-deploy](./nestjs-vercel-deploy) | Build and deploy a Node.js/NestJS application to Vercel |

## Usage

Reference any action in your workflow by path and git ref:

```yaml
- uses: awaismalik01/github-actions/<action-name>@v1
```

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

Wraps `db-flyway` with automatic credential resolution from Infisical. Supports universal, OIDC, and AWS IAM authentication methods.

```yaml
- uses: awaismalik01/github-actions/secure-flyway-migration@v1
  with:
    infisical-client-id: ${{ secrets.INFISICAL_CLIENT_ID }}
    infisical-client-secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
    infisical-project-slug: "my-project"
    infisical-env-slug: "prod"
    db: "billing"
    migration-path: "./migrations/billing"
    schema: "billing"
```

### nestjs-vercel-deploy

Builds a Node.js application and deploys it to Vercel. Supports preview and production deployment targets.

```yaml
- uses: awaismalik01/github-actions/nestjs-vercel-deploy@v1
  with:
    vercel_token: ${{ secrets.VERCEL_TOKEN }}
    vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
    vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
    target: "production"
```

## License

Private repository.
