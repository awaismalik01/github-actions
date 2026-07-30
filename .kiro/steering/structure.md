# Project Structure

```
github-actions/
├── README.md                        # Repo-level overview
├── db-flyway/
│   ├── action.yml                   # Composite action definition
│   └── README.md                    # Usage docs for this action
├── nestjs-vercel-deploy/
│   ├── action.yml
│   └── README.md
├── secure-flyway-migration/
│   ├── action.yml
│   └── README.md
├── vercel-infisical-vault-sync/
│   ├── action.yml
│   └── README.md
└── .kiro/                           # Kiro IDE configuration
    └── steering/                    # Steering rules for AI assistance
```

## Conventions

- Each action lives in its own top-level directory named after the action.
- Every action directory contains exactly:
  - `action.yml` — The composite action definition (inputs, steps).
  - `README.md` — Documentation with usage examples and input tables.
- No shared code or helper scripts exist outside action directories.
- Action names use kebab-case (e.g. `db-flyway`, `secure-flyway-migration`).

## Adding a New Action

1. Create a new top-level directory with a kebab-case name.
2. Add `action.yml` with `runs.using: composite` and well-documented inputs.
3. Add a `README.md` with a description, usage example, and inputs table.
4. Reference the new action from consuming workflows using `awaismalik01/github-actions/<name>@<ref>`.
