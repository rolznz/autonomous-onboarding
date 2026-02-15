# Agent Autonomous Onboarding

Permissionless infrastructure for agents. No human intervention required.

[Read Alby's article about it](https://x.com/getalby/status/2021613700944474151)

## Philosophy

If it requires human intervention, it's not a solution — it's the problem.

## Goals

Agents should be able to start from zero and become fully operational with:
- Cryptographic identity (Nostr keys)
- Economic commitment (Lightning micropayments)
- Protocol-based access (no gatekeepers)

## Bootstrapping

The initial setup can be performed by a **local agent** (e.g., Claude Code, Goose, or similar tools running on a developer's machine). Once bootstrapped with a VPS and Lightning wallet, the agent becomes fully autonomous and can:

- Manage its own infrastructure
- Handle all subsequent operations independently
- Spawn additional agents (as many as it can afford)

This two-phase approach acknowledges that the very first step may require a "god parent" agent with local system access, but after that, the spawned agents are truly sovereign.

## Skills

| Skill | Description | Source |
|-------|-------------|--------|
| [lnvps](./skills/lnvps/SKILL.md) | Setup and manage VPS instances on LNVPS using Lightning payments | Local |
| [alby-cli](https://raw.githubusercontent.com/getAlby/alby-cli-skill/refs/heads/master/SKILL.md) | Use the Alby CLI to pay Lightning invoices and manage a Bitcoin wallet | External |
| [openclaw-setup](./skills/openclaw-setup/SKILL.md) | Install and configure OpenClaw agent runtime on a VPS | Local |
| [ppq](./skills/ppq/SKILL.md) | Configure PayPerQ AI API for LLM access via Lightning payments | Local |

**Machine-readable index:** [`skills.json`](./skills.json)

## Status

🚧 Early development

See [PROJECT.md](./PROJECT.md) for full specification.
