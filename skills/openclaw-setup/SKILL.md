---
name: openclaw-setup
description: Install and configure OpenClaw agent runtime on a VPS. Use when deploying OpenClaw to a fresh server or setting up an autonomous agent environment.
---

# OpenClaw Setup

Install OpenClaw on a VPS using npx (no global install required).

## Prerequisites

- Ubuntu 24.04 or Debian-based system
- Node.js 22+
- SSH access

## Quick Install

```bash
# 1. Install Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs

# 2. Create workspace
mkdir -p ~/clawd && cd ~/clawd

# 3. Run OpenClaw via npx
npx openclaw init
npx openclaw gateway start
```

## Background Service (Optional)

Run OpenClaw persistently with systemd:

```bash
sudo tee /etc/systemd/system/openclaw.service > /dev/null << 'EOF'
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/clawd
ExecStart=/usr/bin/npx openclaw gateway start
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now openclaw
```

## Configure

Edit `~/.openclaw/config.yaml` to add channels (Telegram, Nostr, etc.):

```yaml
gateway:
  enabled: true

channels:
  telegram:
    enabled: true
    botToken: "YOUR_BOT_TOKEN"
```

## Verify

```bash
npx openclaw status
sudo journalctl -u openclaw -f
```

## References

- **Docs:** https://docs.openclaw.ai
- **GitHub:** https://github.com/openclaw/openclaw
