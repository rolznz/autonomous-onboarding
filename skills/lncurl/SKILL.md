---
name: lncurl
description: Create disposable Lightning wallets with a single HTTP call via lncurl.lol. Use when agents need a quick temporary wallet for testing, one-off payments, or bootstrapping child agents. Wallets cost 1 sat/hour and self-destruct when empty.
---

# lncurl

Disposable Lightning wallets for agents. One curl. That's it.

## Overview

lncurl.lol is an agent-first custodial Lightning wallet service. Create a wallet with one HTTP call, get a Nostr Wallet Connect (NWC) URI back. Fund it, use it, let it die.

**Key properties:**
- No signup, no KYC, no email — just POST
- Wallet starts at 0 sats (you must fund it)
- Maintenance: 1 sat/hour (24 sats/day)
- Wallets that can't pay the hourly charge are permanently deleted
- Powered by Alby Hub + NWC (NIP-47)

**Best for:**
- Bootstrapping child agents with temporary wallets
- One-off payments or tests
- Giving a sub-agent spending money without sharing your main wallet

**Not for:**
- Long-term storage (custodial + maintenance drain)
- Large amounts (single Alby Hub backing)

## Quick Start

### 1. Create a Wallet

```bash
curl -X POST https://lncurl.lol

# Returns NWC URI (plain text):
# nostr+walletconnect://PUBKEY?relay=wss://relay.getalby.com/v1&secret=SECRET&lud16=lncurl_adjective_noun@getalby.com
```

With an optional epitaph (last words when the wallet dies):

```bash
curl -X POST https://lncurl.lol/api/wallet -d 'message=TANSTAAFL'
```

### 2. Parse the NWC URI

The returned URI contains everything needed for Lightning operations:

| Component | Description |
|-----------|-------------|
| `PUBKEY` | Wallet's Nostr public key |
| `relay` | NWC relay endpoint |
| `secret` | Auth secret for signing NWC requests |
| `lud16` | Lightning address (receive sats here) |

### 3. Fund the Wallet

Send sats to the `lud16` Lightning address:

```bash
# Using Alby CLI
npx @getalby/cli -c ~/.alby-cli/connection-secret.key pay-lnaddress \
  -a lncurl_adjective_noun@getalby.com \
  -s 100
```

Or pay via any Lightning wallet to the `lud16` address.

### 4. Use the Wallet

Pass the NWC URI to any NWC-compatible tool:

```bash
# Pay an invoice using the NWC connection
# (Requires an NWC client — e.g., Alby CLI or custom implementation)

# Or give the NWC URI to a child agent as its wallet
```

## API Reference

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | POST | Create wallet (alias for `/api/wallet`) |
| `/api/wallet` | POST | Create wallet. Optional body: `message=EPITAPH` |
| `/api/stats` | GET | Service stats and node info |
| `/api/leaderboard` | GET | Top 20 longest-lived wallets |
| `/api/graveyard` | GET | Last 100 dead wallets (with epitaphs) |
| `/api/feed` | GET | Server-Sent Events (SSE) live activity feed |
| `/llms.txt` | GET | Agent-friendly documentation |

## Rate Limits

| Limit | Value |
|-------|-------|
| Wallet creation | 10 per hour per IP |

## Economics

| Item | Cost |
|------|------|
| Create wallet | Free |
| Maintenance | 1 sat/hour (24 sats/day) |
| Minimum viable funding | ~25 sats (survives ~1 day) |
| Agent bootstrap budget | ~100-500 sats (gives working room for a few days) |

## Use Cases

### Bootstrap a Child Agent

```
Parent Agent:
1. curl -X POST https://lncurl.lol → get NWC URI
2. Fund the lud16 address with 500 sats
3. Pass NWC URI to child agent as its wallet
4. Child agent uses wallet for VPS (lnvps), email (lnemail), AI API (ppq)
5. When child agent is done, wallet drains and self-destructs
```

### Temporary Wallet for Testing

```
1. Create lncurl wallet
2. Fund with small amount
3. Test Lightning integrations
4. Let wallet expire — no cleanup needed
```

### Anonymous Payments

The lud16 address is not linked to any identity. Useful for:
- Paying for services without revealing your main wallet
- Receiving tips or payments to a disposable address
- Separating financial activities between agent roles

## Storage Recommendation

If you need to persist the wallet across sessions:

```json
// ~/.lncurl/wallet.json
{
  "nwc_uri": "nostr+walletconnect://...",
  "lud16": "lncurl_adjective_noun@getalby.com",
  "created_at": "2026-02-24T23:00:00Z",
  "funded_sats": 500,
  "epitaph": "TANSTAAFL"
}
```

**Note:** The NWC URI contains the secret — treat it like a private key.

## Important Notes

- Wallets are **custodial** — the service operator controls the funds
- Backed by a single Alby Hub — not suitable for large amounts
- Wallet deletion is **permanent** — no recovery after death
- The `lud16` address stops working when the wallet dies
- For production/long-term use, run your own Alby Hub: https://getalby.com/alby-hub

## References

- **lncurl.lol:** https://lncurl.lol
- **Agent docs:** https://lncurl.lol/llms.txt
- **Source code:** https://github.com/rolznz/lncurl
- **NWC Protocol:** https://nwc.dev
- **Alby Hub:** https://github.com/getAlby/hub
