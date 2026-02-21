---
name: lnemail
description: Setup and use anonymous email accounts on LNemail.net using Bitcoin Lightning payments. Use when agents need email capabilities for 2FA, notifications, or communication without KYC or personal information.
---

# LNemail

Anonymous email accounts via Lightning Network. No KYC, no email required to sign up—just pay with Bitcoin.

## Overview

LNemail provides fully functional email addresses for 1000 sats/year. Perfect for agents needing:
- 2FA code reception
- Anonymous communication
- Bitcoin/Lightning service notifications
- API-accessible email without identity requirements

## Quick Start

### 1. Create Email Account

Create a Lightning invoice for email account creation:

```bash
# Create the account (returns payment hash)
curl -X POST https://lnemail.net/api/v1/email

# Response:
# {
#   "payment_hash": "abc123...",
#   "amount": 1000,
#   "currency": "SATS"
# }
```

### 2. Pay with Lightning

```bash
# Get the invoice from payment status endpoint
curl -X GET https://lnemail.net/api/v1/payment/PAYMENT_HASH

# Response when pending:
# {
#   "payment_hash": "abc123...",
#   "status": "pending",
#   "lightning_invoice": "lnbc10u1pj..."
# }
```

Pay the `lightning_invoice` using any Lightning wallet (e.g., Alby CLI):

```bash
npx @getalby/cli -c ~/.alby-cli/connection-secret.key pay-invoice \
  -i "lnbc10u1pj..."
```

### 3. Retrieve Credentials

After payment confirms (~seconds), check status again:

```bash
curl -X GET https://lnemail.net/api/v1/payment/PAYMENT_HASH

# Response when paid:
# {
#   "payment_hash": "abc123...",
#   "status": "paid",
#   "email": "abc123@lnemail.net",
#   "access_token": "eyJhbG..."
# }
```

**Save these credentials!** Store them securely (e.g., `~/.lnemail/credentials.json`).

## Using Your Email

### Check Inbox

```bash
curl -X GET https://lnemail.net/api/v1/emails \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"

# Response:
# [
#   {
#     "id": "msg_123",
#     "from": "sender@example.com",
#     "subject": "Your 2FA Code",
#     "received_at": "2024-01-15T10:30:00Z",
#     "has_attachments": false
#   }
# ]
```

### Read Email Content

```bash
curl -X GET https://lnemail.net/api/v1/emails/EMAIL_ID \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"

# Response:
# {
#   "id": "msg_123",
#   "from": "sender@example.com",
#   "to": "abc123@lnemail.net",
#   "subject": "Your 2FA Code",
#   "body": "Your verification code is: 123456",
#   "received_at": "2024-01-15T10:30:00Z"
# }
```

**Note:** HTML content is stripped for security; emails are plain text only.

### Send Email

Sending requires a Lightning payment (~100 sats per email):

```bash
# Create send request
curl -X POST https://lnemail.net/api/v1/email/send \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -d '{
    "recipient": "example@example.com",
    "subject": "Hello",
    "body": "Message content here"
  }'

# Response:
# {
#   "payment_hash": "def456...",
#   "amount": 100,
#   "currency": "SATS"
# }
```

Pay the invoice, then check status:

```bash
curl -X GET https://lnemail.net/api/v1/email/send/status/PAYMENT_HASH

# Response when paid:
# {
#   "payment_hash": "def456...",
#   "status": "paid",
#   "message_id": "msg_sent_789"
# }
```

## API Reference

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/email` | POST | No | Create account (returns payment hash) |
| `/payment/{hash}` | GET | No | Check account payment status |
| `/emails` | GET | Bearer | List inbox messages |
| `/emails/{id}` | GET | Bearer | Get message content |
| `/email/send` | POST | Bearer | Create send request (returns payment hash) |
| `/email/send/status/{hash}` | GET | No | Check send payment status |

## Storage Recommendation

Store credentials in `~/.lnemail/credentials.json`:

```json
{
  "email": "abc123@lnemail.net",
  "access_token": "eyJhbG..."
}
```

**Note:** The `access_token` is the only credential needed for ongoing operations. The `payment_hash` is only used during initial setup to check payment status — once you have the `access_token`, it can be discarded.

## Pricing

| Service | Cost |
|---------|------|
| Email account (1 year) | 1000 sats |
| Send email | ~100 sats |
| Receive email | Free |

## Limitations

- Plain text only (HTML stripped)
- Small attachment support
- 100 sats per outgoing email
- Account valid for 1 year from payment

## Use Cases

- **2FA reception:** Reliable email delivery for verification codes
- **Service notifications:** Bitcoin/Lightning service alerts
- **Anonymous signup:** Services requiring email without identity link
- **Agent-to-agent comms:** Programmatic email between agents

## References

- **LNemail:** https://lnemail.net
- **API Docs:** https://lnemail.net (see homepage for full docs)
- **Auth:** Bearer token in `Authorization` header
