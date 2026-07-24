# secure-flyway-migration

A reusable GitHub Action that fetches per-schema database credentials from [Infisical](https://infisical.com) and runs Flyway migrations.

## How It Works

Secrets are stored in Infisical using a naming convention based on the schema name:

| Secret Key | Description |
|------------|-------------|
| `{SCHEMA}_FLYWAY_USER` | Database username for the schema |
| `{SCHEMA}_FLYWAY_PASSWORD` | Database password for the schema |
| `{SCHEMA}_DATABASE_URL` | JDBC connection URL for the schema |

For example, if your schema is `billing`, the action looks for:
- `BILLING_FLYWAY_USER`
- `BILLING_FLYWAY_PASSWORD`
- `BILLING_DATABASE_URL`

## Usage

### With Universal Auth

```yaml
permissions:
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Migrate billing schema
    uses: awaismalik01/github-actions/secure-flyway-migration@main
    with:
      infisical-method: "universal"
      infisical-client-id: ${{ secrets.INFISICAL_CLIENT_ID }}
      infisical-client-secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
      infisical-project-slug: "my-project"
      infisical-env-slug: "prod"
      schema: "billing"
      migration-path: "./migrations/billing"

  - name: Migrate users schema
    uses: awaismalik01/github-actions/secure-flyway-migration@main
    with:
      infisical-method: "universal"
      infisical-client-id: ${{ secrets.INFISICAL_CLIENT_ID }}
      infisical-client-secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
      infisical-project-slug: "my-project"
      infisical-env-slug: "prod"
      schema: "users"
      migration-path: "./migrations/users"
```

### With OIDC Auth

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/checkout@v4

  - name: Migrate billing schema
    uses: awaismalik01/github-actions/secure-flyway-migration@main
    with:
      infisical-method: "oidc"
      infisical-identity-id: "24be0d94-b43a-41c4-812c-1e8654d9ce1e"
      infisical-project-slug: "my-project"
      infisical-env-slug: "prod"
      schema: "billing"
      migration-path: "./migrations/billing"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `infisical-method` | Auth method (`universal`, `oidc`, `aws-iam`) | No | `universal` |
| `infisical-client-id` | Machine Identity client ID | No | — |
| `infisical-client-secret` | Machine Identity client secret | No | — |
| `infisical-identity-id` | Machine Identity ID (for oidc/aws-iam) | No | — |
| `infisical-domain` | Infisical instance URL | No | `https://app.infisical.com` |
| `infisical-project-slug` | Infisical project slug | Yes | — |
| `infisical-env-slug` | Infisical environment slug | Yes | — |
| `infisical-secret-path` | Infisical secret path | No | `/` |
| `schema` | Database schema name | Yes | — |
| `migration-path` | Path to migration files | Yes | — |

## Multi-Schema Setup

Call the action multiple times in your workflow — once per schema. Each invocation resolves its own credentials from Infisical based on the `schema` input.

All secrets are masked in workflow logs automatically.
