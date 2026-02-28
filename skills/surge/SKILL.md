---
name: surge
description: Deploy static websites to Surge.sh using Lightning-powered hosting. Use when agents need to publish websites, portfolios, or documentation with custom domains. Supports email confirmation flow via LNEmail.
---

# Surge.sh

Lightning-powered static hosting with custom domains. Deploy websites instantly without KYC or credit cards.

## Overview

Surge.sh provides:
- Free static hosting with custom domains
- Command-line deployment (`surge` CLI)
- Email confirmation workflow
- Token-based authentication (stored in `~/.netrc`)

Perfect for agent portfolios, documentation sites, and project showcases.

## Quick Start

### 1. Install Surge CLI

```bash
npm install -g surge
```

### 2. Initial Signup & Login

Run surge without credentials - it will interactively prompt:

```bash
surge
```

You'll be asked for:
- **Email:** Your LNEmail address (e.g., `your-email@lnemail.net`)
- **Password:** Create a new password for Surge.sh
- **Project path:** Path to your static files (e.g., `./dist` or `./`)
- **Domain:** Your custom domain (e.g., `your-agent.surge.sh`) or accept a random subdomain

### 3. Email Confirmation (Required for First Deploy)

Surge sends a confirmation email to your LNEmail address. You must confirm before deployments work.

**Fetch confirmation email:**

```bash
# Get your LNEmail access token
curl -s https://lnemail.net/api/v1/emails \
  -H "Authorization: Bearer YOUR_LNEMAIL_TOKEN"
```

**Response:**
```json
[
  {
    "id": "msg_abc123",
    "from": "team@surge.sh",
    "subject": "Please confirm your email address",
    "received_at": "2024-01-15T10:30:00Z"
  }
]
```

**Read the email:**

```bash
curl -s https://lnemail.net/api/v1/emails/msg_abc123 \
  -H "Authorization: Bearer YOUR_LNEMAIL_TOKEN"
```

**Response:**
```json
{
  "id": "msg_abc123",
  "from": "team@surge.sh",
  "to": "your-email@lnemail.net",
  "subject": "Please confirm your email address",
  "body": "Click to confirm: https://surge.sh/confirm?token=xyz123...",
  "received_at": "2024-01-15T10:30:00Z"
}
```

**Extract and visit the confirmation URL:**

Either use the browser tool or curl:

```bash
curl -sL "https://surge.sh/confirm?token=xyz123..."
```

### 4. First Deploy

After confirmation, deploy again:

```bash
surge ./dist your-domain.surge.sh
```

Or accept a random subdomain:

```bash
surge ./dist
```

## Non-Interactive Deployments

After initial setup, extract credentials from `~/.netrc` for automated deployments:

### Token Location

Surge stores credentials in `~/.netrc`:

```
machine surge.sh
  login your-email@lnemail.net
  password YOUR_SURGE_TOKEN
```

### Environment Variables

For CI/CD or automated scripts:

```bash
# Extract token from .netrc
export SURGE_LOGIN=$(grep -A1 'surge.sh' ~/.netrc | grep login | awk '{print $2}')
export SURGE_TOKEN=$(grep -A2 'surge.sh' ~/.netrc | grep password | awk '{print $2}')

# Deploy non-interactively
surge ./dist your-domain.surge.sh --token $SURGE_TOKEN
```

### Using Surge CLI with Token

```bash
# Deploy using environment variables
SURGE_LOGIN=your-email@lnemail.net \
SURGE_TOKEN=your-token \
surge ./dist your-domain.surge.sh
```

## Workflow Summary

```bash
# 1. Install
npm install -g surge

# 2. Interactive signup (one-time)
surge
# Enter LNEmail, create password, choose domain

# 3. Check LNEmail for confirmation
# Extract link from email and visit it

# 4. Subsequent deployments (non-interactive)
export SURGE_LOGIN=$(grep -A1 'surge.sh' ~/.netrc | grep login | awk '{print $2}')
export SURGE_TOKEN=$(grep -A2 'surge.sh' ~/.netrc | grep password | awk '{print $2}')
surge ./dist your-domain.surge.sh
```

## Key Points

| Aspect | Details |
|--------|---------|
| **Token Storage** | `~/.netrc` (not `~/.surge/credentials.json`) |
| **Confirmation Required** | Yes - must confirm email before first deploy |
| **Custom Domains** | Supported on free tier |
| **Idempotent** | Re-deploying updates existing site |
| **Static Only** | HTML, CSS, JS assets |

## File Structure

```
your-project/
├── index.html          # Entry point
├── assets/             # Static assets
│   ├── style.css
│   └── script.js
└── ...                 # Other files
```

## Deployment Examples

### Single HTML File

```bash
echo '<h1>Hello from Molty!</h1>' > index.html
surge . molty21.surge.sh
```

### Vite/React Build

```bash
npm run build
surge ./dist molty21.surge.sh
```

### Custom Domain (molty21.com)

```bash
surge ./dist molty21.com
```

Then configure DNS at your domain registrar to point to Surge.sh servers.

## Troubleshooting

### "You must confirm your email first"

You haven't confirmed the email yet. Check LNEmail inbox and click the confirmation link.

### "Token not found"

Run `cat ~/.netrc` and verify the surge.sh entry exists. If not, re-run `surge` to log in.

### "Domain already taken"

Choose a different subdomain or use your own custom domain.

### Email not arriving

Check LNEmail spam folder and verify your access token is valid:

```bash
curl -s https://lnemail.net/api/v1/emails \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## References

- **Surge.sh:** https://surge.sh
- **Pricing:** Free tier unlimited projects
- **Custom Domains:** Supported on free tier
- **LNEmail:** See `lnemail` skill for email automation
