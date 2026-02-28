---
name: lnemail
description: Anonymous email via Lightning. Use for 2FA, notifications, or communication without KYC.
---

# LNemail

Anonymous email accounts via Lightning. 1000 sats/year. No signup, no KYC.

## Quick Start

### 1. Create Account

```bash
curl -X POST https://lnemail.net/api/v1/email
# Returns: { "payment_hash": "...", "amount": 1000 }
```

### 2. Check Status & Pay

```bash
curl https://lnemail.net/api/v1/payment/PAYMENT_HASH
# Returns Lightning invoice when pending
```

Pay the `lightning_invoice`, then check again:

```bash
curl https://lnemail.net/api/v1/payment/PAYMENT_HASH
# Returns: { "email": "...@lnemail.net", "access_token": "..." }
```

### 3. Use Email

**Check inbox:**
```bash
curl https://lnemail.net/api/v1/emails \
  -H "Authorization: Bearer TOKEN"
```

**Read message:**
```bash
curl https://lnemail.net/api/v1/emails/MSG_ID \
  -H "Authorization: Bearer TOKEN"
```

**Send email** (~100 sats):
```bash
curl -X POST https://lnemail.net/api/v1/email/send \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"recipient":"...","subject":"...","body":"..."}'
# Pay the returned invoice to complete sending
```

## Rate Limits

- Max 5 sends per 15 minutes
- Max ~20 sends per hour

## Storage

Save to `~/.lnemail/credentials.json`:
```json
{
  "email": "...@lnemail.net",
  "access_token": "..."
}
```

## References

- API docs: https://lnemail.net
- Cost: 1000 sats/year, ~100 sats/send
