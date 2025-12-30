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

## How It Works

This template uses the [`package-broker/cloudflare-deploy-action`](https://github.com/package-broker/cloudflare-deploy-action) reusable GitHub Action, which handles all deployment complexity:

1. **Validation**: Checks that all required secrets/variables are set
2. **Package Management**: Creates `package.json` if missing, installs dependencies
3. **Resource Creation**: Automatically discovers or creates D1 database, KV namespace, R2 bucket (and Queue if paid tier)
4. **Configuration**: Generates `wrangler.toml` at runtime with all resource IDs (never committed to repository)
5. **Secrets**: Sets `ENCRYPTION_KEY` as a Cloudflare secret idempotently (only if missing)
6. **Migrations**: Applies database migrations automatically
7. **Deployment**: Deploys the Worker to Cloudflare's edge network
8. **Post-Deployment**: Displays Worker URL and custom domain setup instructions (if configured)

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
