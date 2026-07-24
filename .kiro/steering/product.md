# Product Overview

This repository is a collection of reusable GitHub Actions (composite actions) for CI/CD automation. The actions are published under the `awaismalik01/github-actions` namespace and referenced by consuming workflows via path (e.g. `awaismalik01/github-actions/db-flyway@v1`).

## Current Actions

- **db-flyway** — Runs Flyway database migrations against PostgreSQL.
- **secure-flyway-migration** — Fetches per-schema database credentials from Infisical (secrets manager), then delegates to `db-flyway` for the actual migration.
- **nestjs-vercel-deploy** — Builds a Node.js/NestJS application and deploys it to Vercel (supports preview and production targets).

## Design Philosophy

- Each action is self-contained in its own directory with an `action.yml` and `README.md`.
- Actions compose where possible (`secure-flyway-migration` calls `db-flyway` internally).
- Secrets are never logged; sensitive values are masked with `::add-mask::`.
