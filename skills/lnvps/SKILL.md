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
Visit https://lnvps.net/plans — the Small plan (2 vCPU, 2GB RAM, 80GB SSD) should be sufficient for most agent workloads.

**Option B: Automated browsing with Playwright**
Use browser automation to navigate LNVPS:
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("https://lnvps.net/plans")
    # Select Small plan, configure options
    browser.close()
```

**Order steps:**
1. Select the Small plan (2GB RAM)
2. Select OS (Ubuntu 24.04 recommended)
3. Add SSH key: `cat ~/.ssh/id_ed25519.pub`
4. Click "Create Order"
5. Select payment: Lightning (BTC), LNURL (BTC), or Revolut

### 3. Pay with Lightning

**Get invoice** from the order page (format: `lnbc...`).

**Pay with any Lightning wallet** — copy the invoice and pay. The VM will provision automatically after payment confirms.

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
