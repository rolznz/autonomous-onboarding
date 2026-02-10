---
name: lnvps
description: Setup and manage VPS instances on LNVPS using Lightning payments. Use when provisioning VPS servers, managing VM instances, configuring SSH access, or handling LNVPS account operations including Lightning invoice payments.
---

# LNVPS

Provision and manage VPS instances on LNVPS.net with Bitcoin Lightning payments.

## Quick Start

### 1. Create Account

Navigate to https://lnvps.net, click "Sign In" → "Create Account". No email required—just a display name. NSEC generated automatically—save it immediately to `~/.openclaw/lnvps-nsec`.

### 2. Browse and Buy VPS

**Option A: Manual browsing**
Visit https://lnvps.net/plans, select a plan, click "Buy Now".

**Option B: Automated browsing with Playwright**
Use browser automation to navigate LNVPS:
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("https://lnvps.net/plans")
    # Select plan, configure options
    browser.close()
```

**VPS Plans (Dublin, IE):**
| Plan | CPU | RAM | Disk | Price |
|------|-----|-----|------|-------|
| Tiny | 1 | 1GB | 40GB SSD | €2.70/mo |
| Small | 2 | 2GB | 80GB SSD | €5.10/mo |
| Medium | 4 | 4GB | 160GB SSD | €9.90/mo |
| Large | 8 | 8GB | 400GB SSD | €21.90/mo |

**Order steps:**
1. Select OS (Ubuntu 24.04 recommended)
2. Add SSH key: `cat ~/.ssh/id_ed25519.pub`
3. Click "Create Order"
4. Select payment: Lightning (BTC), LNURL (BTC), or Revolut

### 3. Pay with Lightning

**Get invoice** from the order page (format: `lnbc...`).

**Pay via Alby CLI:**
```bash
npx @getalby/cli pay-invoice -i <INVOICE> -c ~/.alby-cli/connection-secret.key
```

**Example response:**
```json
{
  "preimage": "9b97bf3c5c8ce08d1d5139b105eb4a393f061a6c2bc8b9e3557e3b8fc63bc416",
  "fees_paid_in_sats": 4
}
```

### 4. Access Your VM

**Check status:** Account → My Resources → VPS

**Connection:**
- **DNS:** `vm-{id}.lnvps.cloud`
- **IPv6:** Shown on VM page
- **Username:** `ubuntu` (Ubuntu) / `debian` (Debian)

**SSH:**
```bash
ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ubuntu@vm-{id}.lnvps.cloud
```

## Authentication

| Credential | Location | Format |
|------------|----------|--------|
| NSEC (login) | `~/.openclaw/lnvps-nsec` | `nsec1...` |
| SSH key | `~/.ssh/id_ed25519.pub` | SSH ed25519 |

## API

**Base:** `https://api.lnvps.net`

**Endpoints:**
- `GET /api/v1/account` - Account info
- `GET /api/v1/vm` - List VMs
- `GET /api/v1/vm/{id}/payments` - Payment history

Auth: Nostr-based (requires NSEC signing)

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Invoice expired | Generate new invoice from billing page (~10min expiry) |
| Auth failed | Verify NSEC format starts with `nsec1` |
| VM not starting | Check payment status; allow 1-2min provisioning time |
| SSH refused | Verify SSH key added; check username matches OS |

## References

- **LNVPS:** https://lnvps.net
- **Playwright docs:** For automated browsing workflows
