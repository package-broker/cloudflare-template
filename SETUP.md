# PACKAGE.broker - Cloudflare Workers Setup Guide

Detailed setup instructions for deploying PACKAGE.broker to Cloudflare Workers using this template.

## Prerequisites

- A GitHub account
- A Cloudflare account
- Node.js 18+ (for local development, not required for GitHub Actions)

## Step 1: Create Repository from Template

1. Click the **"Use this template"** button on this repository
2. Choose a name for your new repository
3. Select **Public** or **Private** (both work)
4. Click **"Create repository from template"**

## Step 2: Generate Encryption Key

Generate a secure encryption key:

```bash
openssl rand -base64 32
```

**Save this key** - you'll need it in the next step.

## Step 3: Create Cloudflare API Token

1. Go to https://dash.cloudflare.com/profile/api-tokens
2. Click **"Create Token"**
3. Use **"Edit Cloudflare Workers"** template, or create a custom token with:

   **Required Permissions**:
   - **Workers** → Workers Scripts → **Edit**
   - **D1** → **Edit**
   - **KV** → **Edit**
   - **R2** → **Edit**
   - **Queues** → **Edit** (only if using paid tier)

   **Optional** (recommended):
   - **Account** → Account Settings → **Read**

4. Set **Account Resources** to your account
5. Click **"Continue to summary"** → **"Create Token"**
6. **Copy the token immediately** (you won't see it again)

## Step 4: Get Cloudflare Account ID

1. Go to https://dash.cloudflare.com/
2. Select your account
3. Find **Account ID** in the right sidebar
4. Copy it

## Step 5: Configure GitHub Secrets and Variables

### Secrets (Settings → Secrets and variables → Actions → Secrets)

1. Click **"New repository secret"**
2. Add:
   - **Name**: `CLOUDFLARE_API_TOKEN`
   - **Value**: Paste your API token from Step 3
   - Click **"Add secret"**

3. Click **"New repository secret"** again
4. Add:
   - **Name**: `ENCRYPTION_KEY`
   - **Value**: Paste the encryption key from Step 2
   - Click **"Add secret"**

### Variables (Settings → Secrets and variables → Actions → Variables)

1. Click **"New repository variable"**
2. Add:
   - **Name**: `CLOUDFLARE_ACCOUNT_ID`
   - **Value**: Paste your Account ID from Step 4
   - Click **"Add variable"**

### Optional Variables

- **`WORKER_NAME`**: Custom worker name (defaults to repository name)
- **`CLOUDFLARE_TIER`**: Set to `paid` if you're on Workers Paid plan (defaults to `free`)
- **`CUSTOM_DOMAIN`**: Custom domain to use (e.g., `app.example.com`). After deployment, you'll receive instructions to create a CNAME record in Cloudflare DNS.

## Step 6: Deploy

### Automatic Deployment

Push to the `main` branch:

```bash
git push origin main
```

The workflow uses the [`package-broker/deploy-action`](https://github.com/package-broker/deploy-action) reusable action, which automatically:
- Validates configuration
- Creates `package.json` if missing
- Discovers or creates Cloudflare resources (D1, KV, R2, Queue)
- Generates `wrangler.toml` at runtime (not committed to repository)
- Sets secrets idempotently (only if missing)
- Applies migrations
- Deploys the Worker
- Displays Worker URL and custom domain setup instructions

### Manual Deployment

1. Go to **Actions** tab in your repository
2. Select **"Deploy to Cloudflare Workers"** workflow
3. Click **"Run workflow"**
4. Click **"Run workflow"** button

## Step 7: Complete Initial Setup

1. Check the workflow logs for your Worker URL (e.g., `https://your-worker.workers.dev`)
2. Open the URL in your browser
3. Enter your email address
4. Set a secure password
5. Click **"Complete Setup"**

## Step 8: Create Access Token

1. In the Worker dashboard, navigate to **Access Tokens**
2. Click **"Create Token"**
3. Enter a description
4. Select permissions (readonly or write)
5. Click **"Create"**
6. **Copy the token immediately**

## Troubleshooting

### Workflow Fails: "Missing required CLOUDFLARE_API_TOKEN"

- Verify the secret is set in Settings → Secrets and variables → Actions → Secrets
- Check the secret name is exactly `CLOUDFLARE_API_TOKEN` (case-sensitive)

### Workflow Fails: "Invalid CLOUDFLARE_ACCOUNT_ID"

- Verify the variable is set in Settings → Secrets and variables → Actions → Variables
- Check the Account ID is correct (32-character hex string)

### Deployment Fails: "Authentication failed"

- Verify your API token has the correct permissions
- Check the token hasn't expired
- Ensure the token is scoped to the correct account

### Migrations Fail

- Check the database was created successfully
- Verify migrations directory exists in the workflow
- You can manually apply migrations:
  ```bash
  wrangler d1 migrations apply <worker-name>-db --remote
  ```

### Can't Access Dashboard

- Verify the Worker deployed successfully
- Check Worker logs: `wrangler tail`
- Ensure you're using HTTPS (required for Workers)

## Advanced Configuration

### Custom Domain

To use a custom domain (e.g., `app.example.com`):

1. **Add the domain variable**:
   - Go to Settings → Secrets and variables → Actions → Variables
   - Add variable: `CUSTOM_DOMAIN` = `app.example.com`

2. **Deploy**: The workflow will automatically configure the route in `wrangler.toml`

3. **Create CNAME record** (instructions displayed after deployment):
   - Go to Cloudflare Dashboard → Your Zone → DNS → Records
   - Add CNAME record:
     - **Name**: `app` (subdomain)
     - **Target**: `${worker_name}.${account_subdomain}.workers.dev`
     - **Proxy**: Proxied (orange cloud) or DNS only (grey cloud)

4. **Wait for DNS propagation** (usually a few minutes)

**Note**: The domain must be added to your Cloudflare account first. Wrangler API permissions don't include DNS management, so the CNAME record must be created manually.

### Environment-Specific Deployments

Create separate workflows for staging/production by:
- Using different `WORKER_NAME` variables
- Creating separate secrets for each environment
- Using workflow inputs to select environment

## Security Best Practices

- **`wrangler.toml` is generated at runtime** and never committed (not in repository)
- **ENCRYPTION_KEY is stored as Cloudflare secret**, not in configuration files
- **Rotate encryption keys** periodically
- **Use scoped API tokens** (not Global API Key)
- **Limit token permissions** to minimum required
- **Review workflow logs** regularly for security issues
- **Secrets are idempotent** - existing secrets are never overwritten

## Support

- [Documentation](https://package.broker/docs)
- [GitHub Issues](https://github.com/package-broker/server/issues)
- [GitHub Discussions](https://github.com/orgs/package-broker/discussions)

