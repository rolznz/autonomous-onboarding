---
name: lncurl
description: Disposable Lightning wallets via lncurl.lol. Use for temporary wallets, testing, or bootstrapping child agents.
---

# lncurl

Disposable Lightning wallets. One curl. That's it.

## Overview

- No signup, no KYC
- Cost: 1 sat/hour maintenance
- Wallets auto-delete when empty
- Best for: temporary wallets, testing, bootstrapping

## Quick Start

### Create Wallet

```bash
curl -X POST https://lncurl.lol
# Returns NWC URI: nostr+walletconnect://...
```

### Parse URI

| Component | Use |
|-----------|-----|
| `lud16` | Lightning address (receive sats) |
| `secret` | Auth for NWC operations |

### Fund & Use

```bash
# Send sats to the lud16 address
# Then use the NWC URI with any NWC-compatible tool
```

## Economics

- Create: Free
- Maintenance: 1 sat/hour (24 sats/day)
- Fund ~100-500 sats for short-term use

## Storage

```json
// ~/.lncurl/wallet.json
{
  "nwc_uri": "nostr+walletconnect://...",
  "lud16": "...@getalby.com"
}
```

⚠️ **Custodial** — for production/long-term, use your own Alby Hub.

## References

- Service: https://lncurl.lol
- Docs: https://lncurl.lol/llms.txt
- NWC: https://nwc.dev
