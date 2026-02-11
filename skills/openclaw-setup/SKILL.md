---
name: openclaw-setup
description: Install and configure OpenClaw agent runtime on a VPS. Use when deploying OpenClaw to a fresh server or setting up an autonomous agent environment.
---

# OpenClaw Setup

Install OpenClaw on a VPS using the official installer with non-interactive automation.

## Prerequisites

- Ubuntu 24.04 or Debian-based system
- SSH access
- ~2GB RAM (Small VPS plan sufficient)

## Automated Install

```bash
# 1. Run the official installer (handles Node.js and all dependencies)
curl -fsSL https://openclaw.ai/install.sh | bash

# 2. Add npm global bin to PATH
echo 'export PATH="/home/ubuntu/.npm-global/bin:$PATH"' >> ~/.bashrc
export PATH="/home/ubuntu/.npm-global/bin:$PATH"

# 3. Complete setup (the installer fails here without TTY - this fixes it)
openclaw onboard --non-interactive \
  --mode local \
  --skip-skills
```

## Verification

```bash
# Check installation
openclaw --version

# Check workspace
ls -la ~/.openclaw/
ls -la ~/.openclaw/workspace/
```

## Minimal Configuration

For a truly autonomous agent with no human intervention:

**Don't configure (yet):**
- ❌ Telegram bot (requires human to create bot via @BotFather)
- ❌ Nostr keys (generate programmatically later)
- ❌ Model API keys (add when needed)

**The agent is ready when:**
- ✅ OpenClaw CLI installed
- ✅ Workspace initialized
- ✅ No channels configured (agent will add these autonomously later)

## Background Service (Optional)

Run OpenClaw gateway persistently with systemd:

```bash
sudo tee /etc/systemd/system/openclaw.service > /dev/null << 'EOF'
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu
Environment="PATH=/home/ubuntu/.npm-global/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ExecStart=/home/ubuntu/.npm-global/bin/openclaw gateway start
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now openclaw
```

**Note:** The gateway requires channels to be useful. Start it after configuring at least one channel.

## Adding Channels Later

Configure channels via `openclaw config`:

```bash
# Direct config manipulation (no TTY needed)
openclaw config set channels.telegram.enabled true
openclaw config set channels.telegram.botToken "YOUR_BOT_TOKEN"
```

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `openclaw: command not found` | Add `export PATH="/home/ubuntu/.npm-global/bin:$PATH"` to `~/.bashrc` |
| Gateway won't start | Check `openclaw doctor` for health issues |

## References

- **Docs:** https://docs.openclaw.ai
- **Wizard CLI Automation:** https://docs.openclaw.ai/start/wizard-cli-automation
- **GitHub:** https://github.com/openclaw/openclaw
- **Install Script:** https://openclaw.ai/install.sh
