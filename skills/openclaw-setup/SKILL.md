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

# 2. Complete setup (the installer fails here without TTY - this fixes it)
openclaw onboard --non-interactive \
  --mode local \
  --skip-skills

# 3. Install gateway service
openclaw gateway install
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

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `openclaw: command not found` | Reload shell: `source ~/.bashrc` or open new terminal |
| Gateway won't start | Check `openclaw doctor` for health issues |

## References

- **Docs:** https://docs.openclaw.ai
- **Wizard CLI Automation:** https://docs.openclaw.ai/start/wizard-cli-automation
- **GitHub:** https://github.com/openclaw/openclaw
- **Install Script:** https://openclaw.ai/install.sh
