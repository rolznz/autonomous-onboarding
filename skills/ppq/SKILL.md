---
name: ppq
description: Configure PayPerQ (PPQ) AI API for OpenClaw agents. Use when setting up LLM access via Lightning-paid API with moonshotai/kimi-k2.5 model.
---

# PPQ (PayPerQ) AI Setup

Configure PayPerQ API for agent LLM access. PPQ is OpenAI-compatible and accepts Lightning payments.

## Prerequisites

- Lightning wallet with sats (e.g., Alby)
- VPS with OpenClaw installed
- ~1000 sats for initial topup (~$0.70 USD)

## Account Creation

Create a PPQ account programmatically (no email required):

```bash
# Create account
curl -X POST https://api.ppq.ai/accounts/create

# Response:
# {
#   "success": true,
#   "credit_id": "86cfdbd9-46eb-46e2-9dc9-3bce4e432cea",
#   "api_key": "sk-...",
#   "balance": 0
# }
```

**Save these credentials!** The `api_key` is your OpenAI-compatible API key.

## Top Up Via Lightning

### 1. Create Lightning Invoice

```bash
# Create 1000 sat invoice
curl -X POST https://api.ppq.ai/topup/create/btc-lightning \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"amount": 1000, "currency": "SATS"}'

# Response:
# {
#   "invoice_id": "...",
#   "lightning_invoice": "lnbc...",
#   "checkout_url": "..."
# }
```

### 2. Pay the Invoice

Using Alby CLI:

```bash
npx @getalby/cli -c ~/.alby-cli/connection-secret.key pay-invoice \
  -i "LIGHTNING_INVOICE_HERE"
```

Or pay via any Lightning wallet by scanning the `checkout_url` QR code.

### 3. Verify Balance

```bash
curl -X POST https://api.ppq.ai/credits/balance \
  -H "Content-Type: application/json" \
  -d '{"credit_id":"YOUR_CREDIT_ID"}'

# Response: {"balance": 0.708918}  # USD amount
```

## Configure OpenClaw

PPQ is configured as a custom OpenAI-compatible provider in `~/.openclaw/openclaw.json`:

### 1. Save Credentials to VPS

```bash
mkdir -p ~/.ppq
cat > ~/.ppq/credentials.json << EOF
{
  "credit_id": "YOUR_CREDIT_ID",
  "api_key": "sk-YOUR_API_KEY"
}
EOF
```

### 2. Configure OpenClaw

Edit `~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "model": { "primary": "ppq/moonshotai/kimi-k2.5" }
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "ppq": {
        "baseUrl": "https://api.ppq.ai",
        "apiKey": "${PPQ_API_KEY}",
        "api": "openai-completions",
        "models": [{ "id": "moonshotai/kimi-k2.5", "name": "Kimi K2.5" }]
      }
    }
  },
  "env": {
    "PPQ_API_KEY": "sk-YOUR_API_KEY"
  }
}
```

### 3. Set the Default Model

```bash
openclaw models set ppq/moonshotai/kimi-k2.5
```

## API Endpoints

| Endpoint | Description |
|----------|-------------|
| `POST /accounts/create` | Create new account |
| `POST /topup/create/btc-lightning` | Create Lightning invoice |
| `POST /credits/balance` | Check balance |
| `POST /chat/completions` | Chat completions (OpenAI compatible) |
| `GET /v1/models` | List available models |

## References

- **PPQ Docs:** https://ppq.ai/api-docs
- **Base URL:** https://api.ppq.ai
- **Model:** moonshotai/kimi-k2.5
- **Topup Methods:** btc-lightning, btc, ltc, lbtc, xmr
