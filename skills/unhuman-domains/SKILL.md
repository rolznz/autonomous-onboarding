---
name: unhuman-domains
description: Register and manage domain names via unhuman.domains using Bitcoin Lightning payments. Use when agents need programmatic domain registration, DNS management, or domain renewal via API with L402 payment protocol.
---

# unhuman.domains

Programmatic domain registration for AI agents. Search, register, and manage domains via API. Pay with Bitcoin Lightning using the L402 protocol.

## Overview

unhuman.domains is a domain registrar built for AI agents. All operations are performed via REST API with no KYC, no captchas, and no human intervention required. Payment and authentication use the L402 protocol (HTTP 402 + Bitcoin Lightning invoices).

**Reference:** https://unhuman.domains/llms.txt

## Supported TLDs

com, net, org, io, dev, app, xyz, co, ai

## Quick Start

### 1. Search for Domain Availability

```bash
curl -s "https://unhuman.domains/api/domains/search?q=myproject"
```

Response:
```json
{
  "ok": true,
  "data": {
    "results": [
      { "domain": "myproject.dev", "available": true, "price": 1559 }
    ]
  }
}
```

Prices are in USD cents (1559 = $15.59).

### 2. Start Registration (Get Lightning Invoice)

```bash
curl -s -X POST "https://unhuman.domains/api/domains/myproject.dev/register" \
  -H "Content-Type: application/json" \
  -d '{"email": "you@example.com"}'
```

Response (HTTP 402):
```json
{
  "error": { "code": "payment_required" },
  "macaroon": "eyJ...",
  "invoice": "lnbc1559...",
  "amountSats": 1559
}
```

Save the `macaroon` and `invoice`.

### 3. Pay the Lightning Invoice

**Option A: Using Alby CLI (if you already have a wallet)**

```bash
npx @getalby/cli -c ~/.alby-cli/connection-secret.key pay-invoice -i "lnbc1559..."
# Returns: preimage (hex string)
```

**Option B: Using agent-wallet (for fully autonomous agents)**

```bash
npx unhuman domains register myproject.dev --email you@example.com --wallet
```

**Note:** The `--wallet` flag uses agent-wallet for automatic payment. If you already have a Lightning wallet (like Alby CLI), you don't need agent-wallet—just pay the invoice manually and use the preimage in step 4.

### 4. Complete Registration with Payment Proof

```bash
curl -s -X POST "https://unhuman.domains/api/domains/myproject.dev/register" \
  -H "Content-Type: application/json" \
  -H "Authorization: L402 <macaroon>:<preimage>" \
  -d '{"email": "you@example.com"}'
```

Response:
```json
{
  "ok": true,
  "data": {
    "domain": "myproject.dev",
    "status": "registered",
    "managementToken": "eyJhbGciOi..."
  }
}
```

**Save the `managementToken`** — it is required for all future management operations.

## CLI Alternative (Recommended for Shell Access)

If you can run shell commands, the `unhuman` CLI handles the full flow:

```bash
# Search
npx unhuman domains search myproject

# Register with automatic payment (requires agent-wallet)
npx unhuman domains register myproject.dev --email you@example.com --wallet

# Or register without agent-wallet (manual payment)
npx unhuman domains register myproject.dev --email you@example.com
# Then pay the displayed invoice separately
```

## Managing DNS

After registration, configure DNS records using your management token:

```bash
curl -s -X PUT "https://unhuman.domains/api/domains/myproject.dev/dns" \
  -H "Authorization: Bearer <managementToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "records": [
      { "type": "A", "subdomain": "@", "ip": "76.76.21.21" },
      { "type": "CNAME", "subdomain": "www", "target": "cname.vercel-dns.com" }
    ]
  }'
```

## Renewing a Domain

Renewal uses the same L402 flow as registration, plus the management token:

```bash
# Start renewal
curl -s -X POST "https://unhuman.domains/api/domains/myproject.dev/renew" \
  -H "Content-Type: application/json" \
  -H "X-Management-Token: Bearer <managementToken>" \
  -d '{"period": 1}'

# Pay the invoice, then complete with Authorization header
curl -s -X POST "https://unhuman.domains/api/domains/myproject.dev/renew" \
  -H "Content-Type: application/json" \
  -H "Authorization: L402 <macaroon>:<preimage>" \
  -H "X-Management-Token: Bearer <managementToken>" \
  -d '{"period": 1}'
```

## API Reference

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | /api/domains/search?q={query} | None | Search availability |
| POST | /api/domains/{domain}/register | L402 | Register domain |
| POST | /api/domains/{domain}/renew | L402 + Token | Renew domain |
| GET | /api/domains/{domain}/info | Bearer | Domain info |
| GET | /api/domains/{domain}/dns | Bearer | Get DNS records |
| PUT | /api/domains/{domain}/dns | Bearer | Set DNS records |
| PUT | /api/domains/{domain}/nameservers | Bearer | Update nameservers |

## Authentication Summary

- **None:** Search, check availability, status
- **L402:** Registration and renewal (macaroon + preimage)
- **Bearer token:** All management operations (DNS, info, nameservers)
- **Both L402 + Bearer:** Renewal requires both payment proof and management token

## Storage Recommendation

Store the management token in `~/.unhuman/tokens.json`:

```json
{
  "myproject.dev": "eyJhbGciOi..."
}
```

The CLI stores tokens automatically at `~/.unhuman/tokens.json`.

## Important Notes

- **Email required:** ICANN requires email verification. Use a valid email you can access (consider [lnemail](./lnemail/SKILL.md) for anonymous email).
- **Token expiry:** Management tokens are valid for 1 year.
- **L402 flow:** The API returns HTTP 402 with a Lightning invoice. Pay it, then retry the request with `Authorization: L402 <macaroon>:<preimage>`.
- **Agent wallet optional:** The `--wallet` flag uses agent-wallet for autonomous payment. If you already have a Lightning wallet (Alby CLI, etc.), pay invoices manually instead.

## References

- **Service:** https://unhuman.domains
- **LLMs.txt:** https://unhuman.domains/llms.txt
- **CLI:** `npx unhuman`
- **L402 Protocol:** HTTP 402 + Lightning invoices for API authentication
