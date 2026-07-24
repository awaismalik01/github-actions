# Tech Stack

## Platform

- **GitHub Actions** — All actions are composite actions (`runs.using: composite`).
- **Shell** — Steps use `bash` as the shell for inline scripts.

## Key Technologies & Services

| Area | Technology |
|------|-----------|
| Database migrations | Flyway 9.22.3 (CLI, downloaded at runtime) |
| Secrets management | Infisical (via `Infisical/secrets-action@v1.0.16`) |
| Deployment | Vercel CLI (latest, installed via npm) |
| Node.js | v24 (setup via `actions/setup-node@v7.0.0`) |
| Database | PostgreSQL (JDBC connections) |

## Common Commands

There is no build or test system for this repository itself. The actions are declarative YAML consumed by GitHub Actions runners.

- **Lint YAML (optional):** `yamllint .` or use an editor extension.
- **Test an action locally:** Use [act](https://github.com/nektos/act) to simulate GitHub Actions workflows.
- **Reference an action in a workflow:**
  ```yaml
  uses: awaismalik01/github-actions/<action-name>@<ref>
  ```

## Versioning

Actions are referenced by git ref (branch or tag). Use tags like `@v1` for stable consumption.
