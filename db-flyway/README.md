# db-flyway

A reusable GitHub Action for running Flyway database migrations on PostgreSQL.

## Usage

```yaml
- name: Run Flyway Migration
  uses: awaismalik01/github-actions/db-flyway@main
  with:
    db-url: "jdbc:postgresql://your-host:5432/your-db"
    db-user: "your-username"
    db-password: "your-password"
    migration-path: "./path/to/migrations"
    schema: "your_schema"
```

## Inputs

| Input | Description | Required |
|-------|-------------|----------|
| `db-url` | Database JDBC URL | Yes |
| `db-user` | Database username | Yes |
| `db-password` | Database password | Yes |
| `migration-path` | Path to migration scripts in your repository | Yes |
| `schema` | Database schema for migrations and Flyway history table | Yes |

## Notes

Your repository should contain Flyway migration scripts in the directory specified by `migration-path`. Scripts should follow Flyway naming conventions (e.g., `V1__Description.sql`).
