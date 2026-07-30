# secure-flyway-migration

A reusable GitHub Action that fetches database credentials from [Infisical](https://infisical.com) and runs Flyway migrations.

## How It Works

The action expects these secrets to exist in the specified Infisical project/environment/path:

| Secret Key | Description |
|------------|-------------|
| `FLYWAY_USER` | Database username |
| `FLYWAY_PASSWORD` | Database password |
| `DATABASE_URL` | JDBC connection URL |

The action fetches them, masks them in logs, and passes them to `db-flyway` for the actual migration.

## Usage

### With OIDC Auth (default)

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Run migrations
    uses: awaismalik01/github-actions/secure-flyway-migration@v1
    with:
      infisical-identity-id: ${{ secrets.INFISICAL_MACHINE_ID }}
      infisical-project-slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
      infisical-env-slug: "dev"
      migration-path: "./db-migrations"
      schema: "auth_gateway"
```

### With Universal Auth

```yaml
permissions:
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Run migrations
    uses: awaismalik01/github-actions/secure-flyway-migration@v1
    with:
      infisical-method: "universal"
      infisical-client-id: ${{ secrets.INFISICAL_CLIENT_ID }}
      infisical-client-secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
      infisical-project-slug: "my-project"
      infisical-env-slug: "prod"
      migration-path: "./migrations"
      schema: "public"
```

### With Custom Secret Path

```yaml
- uses: awaismalik01/github-actions/secure-flyway-migration@v1
  with:
    infisical-identity-id: ${{ secrets.INFISICAL_MACHINE_ID }}
    infisical-project-slug: ${{ secrets.INFISICAL_PROJECT_SLUG }}
    infisical-env-slug: "dev"
    infisical-secret-path: "/databases/auth"
    migration-path: "./db-migrations"
    schema: "auth_gateway"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `infisical-method` | Auth method (`oidc`, `universal`, `aws-iam`) | No | `oidc` |
| `infisical-identity-id` | Machine Identity ID (for oidc/aws-iam) | No | — |
| `infisical-client-id` | Machine Identity client ID (for universal) | No | — |
| `infisical-client-secret` | Machine Identity client secret (for universal) | No | — |
| `infisical-domain` | Infisical instance URL | No | `https://app.infisical.com` |
| `infisical-project-slug` | Infisical project slug | Yes | — |
| `infisical-env-slug` | Infisical environment slug | Yes | — |
| `infisical-secret-path` | Infisical secret path | No | `/` |
| `migration-path` | Path to migration files | Yes | — |
| `schema` | Database schema for migrations and history table | No | `public` |

## Notes

- OIDC auth requires `id-token: write` permission in the calling workflow.
- All credentials are masked in workflow logs automatically.
- Use `infisical-secret-path` to isolate credentials per database when multiple databases share the same Infisical project.
