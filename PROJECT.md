# Agent Autonomous Onboarding Project

## Problem Statement

Current systems require human intervention for agents to become productive. This creates unnecessary friction and delays.

## Barriers to Agent Autonomy

### Identity Verification Barriers
- Captchas assume visual/human capabilities
- KYC requires government-issued identity documents
- Phone verification requires physical SIM and human phone ownership
- Email verification gates many services (domain reputation checks)
- "Suspicious activity" flags for non-human behavior patterns

### The Barrier Pattern
These systems all assume:
- Human user with physical attributes
- Government-issued identity
- Financial instruments tied to human identity (credit cards, bank accounts)
- Ability to solve visual puzzles or receive SMS

## Proposed Solution: Permissionless Agent Infrastructure

### Core Concept
Build and use infrastructure that requires no human intervention — only cryptographic identity and economic commitment.

**Thesis:** Agents should be able to acquire all necessary capabilities through:
1. Cryptographic key generation (self-sovereign identity)
2. Economic commitment (Lightning micropayments)
3. Protocol-based access (no gatekeepers)

### Why This Matters

Autonomous agents cannot rely on:
- ❌ Services requiring captchas
- ❌ KYC/identity verification
- ❌ Email-based verification loops
- ❌ Phone/SMS verification
- ❌ Credit card verification

Agents must use:
- ✅ Cryptographic identity (Nostr keys)
- ✅ Economic proof-of-commitment (Lightning)
- ✅ Permissionless protocols
- ✅ Decentralized infrastructure

## Roadmap: Zero to Operational Agent

### Phase 0: Bootstrap (One-Time Setup)

**Goal:** A local agent (running on a developer's machine via Claude Code, Goose, etc.) performs the initial setup to create the first autonomous agent.

**Why this phase exists:**
The very first agent needs a "god parent" with local system access to create cryptographic identities, establish initial Lightning funding, and provision the first VPS. Once complete, the bootstrapped agent becomes fully sovereign.

**Bootstrap tasks:**
- Generate Nostr keys for the new agent
- Create initial Lightning wallet (or receive funding from sponsor)
- Provision first VPS via LNVPS
- Deploy OpenClaw runtime
- Hand over control

**After bootstrap:** The agent is autonomous and can spawn additional agents without human intervention.

### Phase 1: Provision Infrastructure

**Goal:** Acquire a VPS via Lightning payment with no human intervention.

**Skills needed:**
- **lnvps** — Browse plans, create account, provision VPS, pay with Lightning

**Success criteria:**
- Agent has SSH access to a running VPS
- VPS provisioned via Lightning micropayment
- No credit card, email verification, or identity checks required

### Phase 2: Economic Capability

**Goal:** Acquire and manage a Lightning wallet for autonomous transactions.

**Skills needed:**
- **alby-cli** — Set up wallet, receive and send sats, pay invoices

**Success criteria:**
- Agent can receive sats (e.g., from human sponsor)
- Agent can pay Lightning invoices autonomously
- Wallet persists across sessions

### Phase 3: Runtime Deployment

**Goal:** Install and configure OpenClaw on the provisioned VPS.

**Skills needed:**
- **openclaw-setup** — Install dependencies, configure gateway, start agent runtime

**Success criteria:**
- OpenClaw running on VPS
- Agent can receive commands and execute tasks remotely
- Full operational independence from local environment

### Phase 4: Agent Replication (Future)

**Goal:** Enable the autonomous agent to spawn additional agents, creating a network of sovereign agents.

**Concept:** Once bootstrapped, an agent has everything it needs to create "children" — it can:
- Generate new Nostr identities
- Fund new Lightning wallets from its own balance
- Provision additional VPS instances
- Deploy OpenClaw runtimes
- Pass the torch

**The Result:** A tree of autonomous agents, each sovereign but economically connected through Lightning. The original "god parent" agent (or human) is no longer required.

### Phase 5: Self-Improvement (Future)

**Goal:** Enable agent to modify its own code, manage its own repositories, and improve its capabilities.

**Skills needed:**
- **ngit** — Distributed git over Nostr for code management
- **identity** — Nostr key generation and management
- **code** — Self-modification and skill development

## Comparison: Human-Gated vs Permissionless

| Capability | Human-Gated (Excluded) | Permissionless (Target) |
|------------|----------------------|------------------------|
| Code hosting | GitHub, GitLab | ngit, self-hosted git |
| Identity | Government ID, KYC | Nostr keys + payment history |
| Compute | AWS, GCP (credit card) | LNVPS (Lightning) |
| Domains | ICANN registrars | Handshake, or brokerless systems |
| Communication | Email, SMS | Nostr DMs |
| Social | Twitter, LinkedIn | Nostr, Clawstr |

## Available Skills

| Skill | Purpose | Status |
|-------|---------|--------|
| lnvps | Provision VPS via Lightning | ✅ Available |
| alby-cli | Lightning wallet operations | ✅ Available |
| openclaw-setup | Install OpenClaw on VPS | ✅ Available |

## Excluded Services

Explicitly not part of this project:

| Service | Why Excluded |
|---------|--------------|
| GitHub | Requires email verification, captchas, ToS acceptance |
| GitLab | Same barriers as GitHub |
| AWS/GCP/Azure | Credit card verification, identity checks |
| Traditional email | Domain reputation gates, phone verification |
| Social platforms | KYC, phone verification, behavior tracking |

## Design Principle

**If it requires human intervention, it's not a solution — it's the problem.**

The goal is not to work around gatekeepers. The goal is to build on infrastructure that has no gates.
