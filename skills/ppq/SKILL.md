---
name: ppq
description: PayPerQ AI API via Lightning. Use for LLM access without subscriptions or API key management.
---

# PPQ (PayPerQ)

Pay-per-use AI API. OpenAI-compatible. No subscription—just pay with Lightning.

## Quick Start

### 1. Create Account

```bash
curl -X POST https://api.ppq.ai/accounts/create
# Returns: { "credit_id": "...", "api_key": "sk-..." }
```

### 2. Top Up

```bash
# Create invoice
curl -X POST https://api.ppq.ai/topup/create/btc-lightning \
  -H "Authorization: Bearer API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"amount": 1000, "currency": "SATS"}'

# Pay the lightning_invoice, then verify:
curl -X POST https://api.ppq.ai/credits/balance \
  -d '{"credit_id":"CREDIT_ID"}'
```

### 3. Configure OpenClaw

Save credentials:
```bash
mkdir -p ~/.ppq
echo '{"credit_id":"...","api_key":"sk-..."}' > ~/.ppq/credentials.json
```

Edit `~/.openclaw/openclaw.json`:
```json
{
  "agents": { "defaults": { "model": { "primary": "ppq/moonshotai/kimi-k2.5" } } },
  "models": {
    "providers": {
      "ppq": {
        "baseUrl": "https://api.ppq.ai",
        "apiKey": "${PPQ_API_KEY}",
        "api": "openai-completions"
      }
    }
  },
  "env": { "PPQ_API_KEY": "sk-..." }
}
```

Set default and restart:
```bash
openclaw models set ppq/moonshotai/kimi-k2.5
openclaw gateway restart
```

## Cost

~1000 sats = ~$0.70 USD. Pay only for what you use.

## References

- Docs: https://ppq.ai/api-docs
- LLMs: https://ppq.ai/llms.txt
- Base URL: https://api.ppq.ai
- Model: moonshotai/kimi-k2.5
