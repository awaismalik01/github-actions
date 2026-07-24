# nestjs-vercel-deploy

A reusable GitHub Action that builds a Node.js/NestJS application and deploys it to Vercel.

## Features

- Sets up Node.js 24
- Installs Vercel CLI
- Installs project dependencies
- Builds the application
- Pulls Vercel project settings
- Builds Vercel artifacts and deploys (supports preview and production targets)

## Usage

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Deploy to Vercel
    uses: awaismalik01/github-actions/nestjs-vercel-deploy@v1
    with:
      vercel_token: ${{ secrets.VERCEL_TOKEN }}
      vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
      vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
      target: "production"
```

### Preview Deployment

```yaml
- name: Deploy preview
  uses: awaismalik01/github-actions/nestjs-vercel-deploy@v1
  with:
    vercel_token: ${{ secrets.VERCEL_TOKEN }}
    vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
    vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
    target: "preview"
```

### Custom Build Commands

```yaml
- name: Deploy with custom commands
  uses: awaismalik01/github-actions/nestjs-vercel-deploy@v1
  with:
    vercel_token: ${{ secrets.VERCEL_TOKEN }}
    vercel_org_id: ${{ secrets.VERCEL_ORG_ID }}
    vercel_project_id: ${{ secrets.VERCEL_PROJECT_ID }}
    target: "production"
    install_command: "yarn install --frozen-lockfile"
    build_command: "yarn build"
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

## How It Works

1. Sets up Node.js 24 using `actions/setup-node@v7.0.0`.
2. Installs the latest Vercel CLI globally.
3. Runs the install command to fetch project dependencies.
4. Runs the build command to compile the application.
5. Pulls Vercel project settings for the specified environment.
6. Builds Vercel deployment artifacts (with `--prod` flag for production).
7. Deploys the prebuilt artifacts to Vercel.
