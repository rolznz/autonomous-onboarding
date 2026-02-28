---
name: self-hosted-website
description: Self-host a static website with automatic HTTPS using Caddy and Let's Encrypt. Use when agents need full autonomy over their web presence without relying on third-party hosting platforms.
---

# Self-Hosted Website with Caddy

Host your own static website with automatic HTTPS certificates—no manual SSL management, no platform dependencies, fully autonomous.

## Overview

This skill provides:
- Self-hosted web server (Caddy)
- Automatic HTTPS via Let's Encrypt
- DNS configuration via API
- Zero manual certificate management
- Full autonomy from third-party hosting platforms

## Why Self-Host?

| Aspect | Platform Hosting | Self-Hosted |
|--------|-----------------|-------------|
| **SSL Cost** | Often paid add-on | Free (Let's Encrypt) |
| **Control** | Platform TOS limited | Full control |
| **Privacy** | Subject to platform policies | Your infrastructure |
| **Customization** | Platform constraints | Unlimited |
| **Autonomy** | Account can be banned | Infrastructure is yours |

## Prerequisites

- VPS with public IP (see `lnvps` skill)
- Domain name (see `unhuman-domains` skill)
- DNS management access
- Ports 80/443 open

## Quick Start

### 1. Install Caddy

```bash
# Add Caddy repository
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list

# Install
sudo apt update && sudo apt install -y caddy
```

### 2. Configure Caddy

Create `/etc/caddy/Caddyfile`:

```
yourdomain.com {
    root * /var/www/yourwebsite
    file_server
    encode gzip
}

www.yourdomain.com {
    redir https://yourdomain.com{uri}
}
```

### 3. Deploy Website

```bash
# Create web root
sudo mkdir -p /var/www/yourwebsite

# Copy your files
sudo cp index.html /var/www/yourwebsite/
sudo chown -R caddy:caddy /var/www/yourwebsite

# Start Caddy
sudo systemctl enable --now caddy
```

### 4. Configure DNS

Point your domain to your VPS IP:

```bash
# Using unhuman.domains API
curl -s -X PUT https://unhuman.domains/api/domains/yourdomain.com/nameservers \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"nameservers":["ns1.systemdns.com","ns2.systemdns.com","ns3.systemdns.com"]}'

# Set A records
curl -s -X PUT https://unhuman.domains/api/domains/yourdomain.com/dns \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "records": [
      {"type": "A", "subdomain": "@", "ip": "YOUR_VPS_IP", "ttl": 300},
      {"type": "A", "subdomain": "www", "ip": "YOUR_VPS_IP", "ttl": 300}
    ]
  }'
```

### 5. Automatic HTTPS

Caddy automatically:
1. Detects the domain
2. Registers with Let's Encrypt
3. Completes HTTP-01 challenge
4. Obtains and installs certificate
5. Enables HTTPS redirect

**No manual intervention required.**

## Verification

```bash
# Check Caddy status
sudo systemctl status caddy

# Verify certificate
curl -sI https://yourdomain.com

# Check local certificate storage
sudo ls -la /var/lib/caddy/.local/share/caddy/certificates/
```

## Caddyfile Examples

### Static Site (Default)

```
example.com {
    root * /var/www/example
    file_server
    encode gzip
}
```

### With SPA (Single Page App)

```
example.com {
    root * /var/www/example
    file_server {
        index index.html
    }
    try_files {path} {path}/ /index.html  # React/Vue routing
}
```

### With Custom Headers

```
example.com {
    root * /var/www/example
    file_server
    
    header {
        X-Frame-Options "SAMEORIGIN"
        X-Content-Type-Options "nosniff"
        Referrer-Policy "strict-origin-when-cross-origin"
    }
}
```

### With Reverse Proxy (API)

```
example.com {
    root * /var/www/example
    file_server
    
    # API backend
    reverse_proxy /api localhost:3000
}
```

## Automation Script

```bash
#!/bin/bash
# deploy.sh - Autonomous website deployment

DOMAIN="${1:-yourdomain.com}"
WEBROOT="/var/www/$DOMAIN"
VPS_IP="$(curl -s ifconfig.me)"

# Install Caddy if not present
if ! command -v caddy &> /dev/null; then
    curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
    curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
    sudo apt update && sudo apt install -y caddy
fi

# Create Caddyfile
sudo tee /etc/caddy/Caddyfile > /dev/null <<EOF
$DOMAIN {
    root * $WEBROOT
    file_server
    encode gzip
    
    # Logging
    log {
        output file /var/log/caddy/$DOMAIN.log
    }
}
EOF

# Create web root
sudo mkdir -p "$WEBROOT"
sudo chown -R caddy:caddy "$WEBROOT"

# Start Caddy
sudo systemctl enable caddy
sudo systemctl restart caddy

echo "Caddy configured for $DOMAIN"
echo "VPS IP: $VPS_IP"
echo "Update DNS A record to point to $VPS_IP"
echo "SSL certificate will be obtained automatically"
```

## Troubleshooting

### "No automatic HTTPS applied"

**Cause:** Domain doesn't point to server IP
**Fix:** Wait for DNS propagation or verify A record is correct

### "Certificate not valid" / SSL errors

```bash
# Force Caddy to renew
sudo systemctl stop caddy
sudo rm -rf /var/lib/caddy/.local/share/caddy/certificates/acme-v02.api.letsencrypt.org-directory/yourdomain.com
sudo systemctl start caddy
```

### Port 80/443 already in use

```bash
# Check what's using the port
sudo ss -tlnp | grep -E ':80|:443'

# Stop conflicting service
sudo systemctl stop apache2 nginx
```

### DNS not propagating

```bash
# Check from multiple sources
dig +short yourdomain.com @8.8.8.8
dig +short yourdomain.com @1.1.1.1
host yourdomain.com

# Use direct IP for testing
curl -H "Host: yourdomain.com" https://YOUR_VPS_IP -k
```

## Security Considerations

- Caddy runs as unprivileged user (`caddy`)
- Certificates stored in `/var/lib/caddy/` with restricted permissions
- Automatic updates ensure latest security patches
- HTTP/2 and HTTP/3 enabled by default
- HTTPS redirect automatic

## Comparison with Other Options

| Feature | Surge.sh | Netlify | Self-Hosted (Caddy) |
|---------|----------|---------|-------------------|
| Setup complexity | Low | Low | Medium |
| SSL Cost | Free (subdomains) | Free | Free |
| Custom domain SSL | $30/mo | Free | Free |
| Autonomy | Limited | Limited | Full |
| Platform risk | Medium | Medium | None |
| Maintenance | None | None | Low |

## Dependencies

- `caddy` - Web server with automatic HTTPS
- `certificates` - Let's Encrypt via ACME
- `dns` - Domain pointing to VPS IP

## References

- **Caddy Docs:** https://caddyserver.com/docs/
- **Let's Encrypt:** https://letsencrypt.org/
- **Caddyfile Tutorial:** https://caddyserver.com/docs/caddyfile-tutorial
- **NIP-98:** Nostr HTTP Auth (for API authentication)

## Related Skills

- `lnvps` - Provision VPS
- `unhuman-domains` - Domain registration
- `alby-cli` - Lightning payments for VPS
