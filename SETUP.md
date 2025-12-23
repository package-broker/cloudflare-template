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

## Step 6: Deploy

### Automatic Deployment

Push to the `main` branch:

```bash
git push origin main
```

The workflow will automatically:
- Validate configuration
- Create Cloudflare resources
- Generate `wrangler.toml`
- Set secrets
- Apply migrations
- Deploy the Worker

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

1. Add your domain to Cloudflare
2. Create a CNAME record pointing to your Worker
3. Update your Worker route in Cloudflare dashboard

### Environment-Specific Deployments

Create separate workflows for staging/production by:
- Using different `WORKER_NAME` variables
- Creating separate secrets for each environment
- Using workflow inputs to select environment

## Security Best Practices

- **Never commit** `wrangler.toml` with secrets (it's in `.gitignore`)
- **Rotate encryption keys** periodically
- **Use scoped API tokens** (not Global API Key)
- **Limit token permissions** to minimum required
- **Review workflow logs** regularly for security issues

## Support

- [Documentation](https://package.broker/docs)
- [GitHub Issues](https://github.com/package-broker/server/issues)
- [GitHub Discussions](https://github.com/orgs/package-broker/discussions)

