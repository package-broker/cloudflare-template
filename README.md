# PACKAGE.broker - Cloudflare Workers Deployment

One-click deployment of PACKAGE.broker to Cloudflare Workers using GitHub Actions.

## Quick Start

1. **Use this template**: Click "Use this template" button above to create your repository
2. **Set required secrets and variables**: Go to Settings → Secrets and variables → Actions
3. **Push to main branch** or manually trigger the workflow
4. **Done!** Your Worker will be deployed automatically

## Required Configuration

### GitHub Secrets (Settings → Secrets and variables → Actions → Secrets)

- **`CLOUDFLARE_API_TOKEN`** - Your Cloudflare API token
  - Create at: https://dash.cloudflare.com/profile/api-tokens
  - **Required permissions**: See [API Token Permissions](#api-token-permissions) below
- **`ENCRYPTION_KEY`** - Base64-encoded 32-byte encryption key
  - Generate with: `openssl rand -base64 32`

### GitHub Variables (Settings → Secrets and variables → Actions → Variables)

- **`CLOUDFLARE_ACCOUNT_ID`** - Your Cloudflare account ID
  - Found in Cloudflare dashboard (right sidebar)

### Optional Variables

- **`WORKER_NAME`** - Custom worker name (defaults to repository name)
- **`CLOUDFLARE_TIER`** - `free` or `paid` (defaults to `free`)
- **`CUSTOM_DOMAIN`** - Custom domain to use (e.g., `app.example.com`). After deployment, you'll receive instructions to create a CNAME record in Cloudflare DNS.

## API Token Permissions

For complete and up-to-date API token permission requirements, see the [Cloudflare API Token Permissions](https://package.broker/docs/deployment/cloudflare-api-token-permissions) documentation.

**Quick Reference**: Your `CLOUDFLARE_API_TOKEN` must be a **scoped API token** (not Global API Key) with **Edit** permissions for:
- **Workers** - Workers Scripts
- **D1** - Databases
- **KV** - Namespaces
- **R2** - Buckets
- **Queues** - Queues (paid tier only)

See the [full documentation](https://package.broker/docs/deployment/cloudflare-api-token-permissions) for detailed permission requirements, security notes, and troubleshooting.

## Prerequisites

**Important**: This template requires an initialized repository with committed `package.json` and `package-lock.json` files. The template repository already includes these files with all required dependencies.

If you're creating a new repository from scratch (not using this template), you must:

1. Initialize the repository:
   ```bash
   npm init -y
   npm install @package-broker/main @package-broker/ui @package-broker/cloudflare
   ```
2. Commit both `package.json` and `package-lock.json` to your repository before using the action.

## How It Works

This template uses the [`package-broker/cloudflare-deploy-action`](https://github.com/package-broker/cloudflare-deploy-action) reusable GitHub Action, which is a thin wrapper around the `@package-broker/cloudflare` CLI tool:

1. **Validation**: Checks that all required secrets/variables are set
2. **Dependency Installation**: Uses `npm ci` with the committed `package-lock.json` for reproducible installs
3. **CLI Deployment**: Calls `@package-broker/cloudflare deploy --ci --json` which handles:
   - Resource discovery/creation (D1, KV, R2, Queue if paid tier)
   - `wrangler.toml` generation at runtime (never committed to repository)
   - Secret management (sets `ENCRYPTION_KEY` as Cloudflare secret)
   - Migration application
   - Worker deployment
4. **Output Parsing**: Extracts deployment results (Worker URL, resource IDs) from CLI JSON output

**Note**: The `wrangler.toml` file is generated automatically during deployment and is not stored in the repository. This ensures it always matches your Cloudflare resources.

## After Deployment

1. Open your Worker URL (shown in workflow logs)
2. Complete initial setup (email + password)
3. Create an access token in the dashboard
4. Start adding repository sources

### Custom Domain Setup

If you configured `CUSTOM_DOMAIN`, the workflow will display step-by-step instructions for creating a CNAME record in Cloudflare DNS. The CNAME should point to your Worker's `workers.dev` URL.

## Documentation

For complete setup instructions, troubleshooting, and advanced configuration, visit:

- **[Cloudflare Workers Deployment Guide](https://package.broker/docs/deployment/cloudflare)**
- [Full Documentation](https://package.broker/docs)
