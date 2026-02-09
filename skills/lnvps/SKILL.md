# LNVPS

Setup and manage VPS instances on LNVPS using Lightning payments.

## Quick Start

### 1. Create Account

```bash
# Navigate to LNVPS and click "Sign In"
# Then "Create Account" - no email required, just a display name
# NSEC will be generated - SAVE IT IMMEDIATELY
```

**Account created with:**
- Display name: `agent-molty`
- Nostr identity automatically generated
- Login uses NSEC (no password)

### 2. Buy VPS

**VPS Plans (Dublin, IE):**
| Plan | CPU | RAM | Disk | Price |
|------|-----|-----|------|-------|
| Tiny | 1 | 1GB | 40GB SSD | €2.70/mo |
| Small | 2 | 2GB | 80GB SSD | €5.10/mo |
| Medium | 4 | 4GB | 160GB SSD | €9.90/mo |
| Large | 8 | 8GB | 400GB SSD | €21.90/mo |

**Steps:**
1. Select plan → Click "Buy Now"
2. Choose OS (Ubuntu 24.04 recommended)
3. Add SSH key:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   # Paste into "Add SSH Key" textarea
   # Name it (e.g., "molty-main")
   ```
4. Click "Create Order"
5. Select payment method:
   - **Lightning (BTC)** - Recommended
   - **LNURL (BTC)** - Alternative
   - **Revolut** - Card payment

### 3. Pay with Lightning

**Get Invoice:**
```bash
# Click "Get Invoice" button
# Invoice appears (e.g., lnbc86518390p1p5c58ghpp58p4...)
```

**Pay via Alby CLI:**
```bash
# Check balance
npx @getalby/cli get-balance -c ~/.alby-cli/connection-secret.key

# Pay invoice
npx @getalby/cli pay-invoice -i <INVOICE> -c ~/.alby-cli/connection-secret.key
```

**Example Output:**
```json
{
  "preimage": "9b97bf3c5c8ce08d1d5139b105eb4a393f061a6c2bc8b9e3557e3b8fc63bc416",
  "fees_paid_in_sats": 4
}
```

### 4. Check VM Status

```bash
# Go to Account → My Resources → VPS
# Status: Running | Stopped | Expired
```

**VM Details:**
- VM ID: #1001
- Plan: Small (2 vCPU, 2GB RAM, 80GB SSD)
- OS: Ubuntu 24.04
- Region: Dublin (IE)
- SSH Key: molty-main

## Authentication

**Login:**
- **Method:** NSEC (Nostr secret key)
- **Location:** Stored in `~/.openclaw/lnvps-nsec`
- **Format:** `nsec1...`

**Session:**
- No traditional session tokens
- Uses Nostr key pair for auth
- Public key: `npub1rpshdta...`

## API Endpoints

**Base URL:** `https://api.lnvps.net`

**Key Endpoints:**
- `GET /api/v1/account` - Account info
- `GET /api/v1/vm` - List VMs
- `GET /api/v1/vm/{id}/payments` - Payment history
- `GET /api/v1/payment/methods` - Available payment methods

**Authentication:** Requires Nostr-based auth header (not fully documented)

## Files and Credentials

| File | Purpose |
|------|---------|
| `~/.openclaw/lnvps-nsec` | Nostr secret key for login |
| `~/.ssh/id_ed25519.pub` | SSH key for VM access |

## Troubleshooting

**Invoice expired:**
- Generate new invoice from billing page
- Previous invoices expire after ~10 minutes

**Auth failed:**
- Verify NSEC is correct
- NSEC format: starts with `nsec1`

**VM not starting:**
- Check payment status on account page
- May take 1-2 minutes to provision after payment

## Roadmap

- [ ] SSH access instructions
- [ ] Automated provisioning scripts
- [ ] NWC (Nostr Wallet Connect) integration for automatic renewal

## References

- **LNVPS:** https://lnvps.net
- **API Docs:** Not publicly documented, reverse-engineered from browser

---

*Created based on agent-molty's first autonomous VPS provisioning*