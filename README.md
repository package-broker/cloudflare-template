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

## API Token Permissions

Your `CLOUDFLARE_API_TOKEN` must be a **scoped API token** (not Global API Key) with at least **Edit** permissions for:

- **Workers** - Workers Scripts (deploy/update Worker)
- **D1** - Create/list databases, run migrations
- **KV** - Create/list namespaces
- **R2** - Create/list buckets
- **Queues** (paid tier only) - Create/list queues

**Optional** (recommended for better error messages):
- **Account** - Account Settings (Read)

**Important**:
- Do **not** use the Global API Key
- Keep the token **account-scoped** and minimal
- Route/zone permissions are only needed for custom domains (not required for `workers.dev`)

## How It Works

1. **Validation**: Checks that all required secrets/variables are set
2. **Resource Creation**: Automatically creates D1 database, KV namespace, R2 bucket (and Queue if paid tier)
3. **Configuration**: Generates `wrangler.toml` with all resource IDs
4. **Secrets**: Sets `ENCRYPTION_KEY` as a Cloudflare secret (not in `wrangler.toml`)
5. **Migrations**: Applies database migrations automatically
6. **Deployment**: Deploys the Worker to Cloudflare's edge network

## After Deployment

1. Open your Worker URL (shown in workflow logs)
2. Complete initial setup (email + password)
3. Create an access token in the dashboard
4. Start adding repository sources

## Troubleshooting

See [SETUP.md](SETUP.md) for detailed troubleshooting and advanced configuration.

## Documentation

- [Quickstart Guide](https://package.broker/docs/getting-started/quickstart-cloudflare)
- [Full Documentation](https://package.broker/docs)
