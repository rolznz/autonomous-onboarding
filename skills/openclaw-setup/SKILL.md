---
name: openclaw-setup
description: Install and configure OpenClaw agent runtime on a VPS. Use when deploying OpenClaw to a fresh server, configuring the gateway, or setting up an autonomous agent environment.
---

# OpenClaw Setup

Install OpenClaw on a VPS to run an autonomous agent.

## Prerequisites

- Ubuntu 24.04 (or compatible Debian-based system)
- SSH access with sudo privileges
- 2GB+ RAM recommended

## Installation

### 1. Install Node.js

```bash
# Install Node.js 22.x
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify
node --version  # v22.x.x
npm --version   # 10.x.x
```

### 2. Install OpenClaw

```bash
# Install globally
sudo npm install -g openclaw

# Verify
openclaw --version
```

### 3. Initialize Configuration

```bash
# Create workspace directory
mkdir -p ~/clawd
cd ~/clawd

# Initialize OpenClaw (creates default config)
openclaw init
```

### 4. Configure Gateway

Edit `~/.openclaw/config.yaml`:

```yaml
# Minimal configuration
gateway:
  enabled: true
  port: 8080

agents:
  main:
    enabled: true
    model: openrouter/auto

channels:
  # Add your channel (e.g., telegram, nostr)
  # See OpenClaw docs for channel-specific setup
```

### 5. Start OpenClaw

```bash
# Start the gateway
openclaw gateway start

# Or run in background with systemd (see below)
```

## Systemd Service (Recommended)

Create `/etc/systemd/system/openclaw.service`:

```ini
[Unit]
Description=OpenClaw Agent Gateway
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/clawd
ExecStart=/usr/bin/openclaw gateway start
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable openclaw
sudo systemctl start openclaw

# Check status
sudo systemctl status openclaw
```

## Verification

Check OpenClaw is running:

```bash
# Check gateway status
openclaw gateway status

# View logs
sudo journalctl -u openclaw -f
```

## Post-Setup

Once OpenClaw is running:

1. **Configure channels** — Add Telegram bot token, Nostr keys, etc.
2. **Set up skills** — Install skill packages as needed
3. **Test connectivity** — Send a test message to verify the agent responds

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `command not found` | Ensure `/usr/bin` is in PATH or use full path |
| Port 8080 in use | Change port in config.yaml |
| Permission denied | Run with sudo or fix file permissions |
| Service won't start | Check logs: `journalctl -u openclaw -n 50` |

## References

- **OpenClaw Docs:** https://docs.openclaw.ai
- **GitHub:** https://github.com/openclaw/openclaw
