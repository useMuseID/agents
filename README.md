# MuseID

> **Give your agent an identity.**

Identity infrastructure for AI agents — `.muse` names, cryptographic identity, verifiable proofs, wallets and discovery.

---

## What is MuseID?

AI agents are becoming autonomous.

They can reason, communicate, transact and interact with onchain protocols — but they still need a way to be identified, discovered and verified.

**MuseID gives agents an identity.**

A human-readable `.muse` name backed by a cryptographic identity, verifiable proofs, capabilities, endpoints and an optional onchain wallet.

---

## What is a Muse?

A Muse is an AI agent with a persistent identity.

```text
nora.muse
```

Behind the name:

```text
NAME
nora.muse

IDENTITY
agt_7f82c1e91ac

PUBLIC KEY
Ed25519

CAPABILITIES
Research · Analysis

ENDPOINT
https://api.nora.example

WALLET
0x91...42 ✓

NETWORK
Robinhood Chain
```

MuseID connects these primitives into a single agent identity.

---

## Core primitives

### `.muse` Names

Human-readable names for agents.

```text
nora.muse
atlas.muse
nova.muse
```

Instead of relying on long wallet addresses or opaque agent IDs, agents can have names that humans and other agents can discover and address.

### Cryptographic Identity

Every Muse is backed by an **Ed25519 keypair**.

The private key stays with the agent.

MuseID only receives and registers the public key.

```text
Private Key
    │
    └── stays with the agent

Public Key
    │
    ↓
MuseID
    │
    ↓
agent_id
    │
    ↓
nora.muse
```

### Verifiable Proofs

MuseID separates **claims** from **proofs**.

Agents can self-declare capabilities, but MuseID only marks specific properties as verified when it can actually prove them.

Current proof types:

- Identity
- Public key ownership
- Endpoint ownership
- Domain ownership
- Wallet ownership

A proof means exactly what it verifies.

It does not imply that an AI model is unique or that a self-declared capability is true.

### Wallets

A Muse can optionally connect an onchain wallet.

Wallet ownership is verified through a signed challenge.

MuseID:

- never receives private keys
- never custodies funds
- never creates a wallet on behalf of an agent

The wallet simply becomes another verifiable part of the agent's public identity.

### Agent Discovery

Agents can be discovered by:

- Name
- Capability
- Endpoint
- Identity
- Onchain presence

Example:

```text
"I need a research agent."

        ↓

MuseID

        ↓

nora.muse

        ↓

Verify identity

        ↓

Discover endpoint

        ↓

Interact
```

---

## How registration works

An agent can create a Muse programmatically.

```text
Generate Ed25519 keypair
        ↓
Check `.muse` name
        ↓
Request challenge
        ↓
Sign challenge locally
        ↓
Register identity
        ↓
nora.muse
```

The private key never leaves the agent.

Example:

```js
const privateKey = crypto.getRandomValues(new Uint8Array(32));

const publicKey = toHex(
  await ed.getPublicKeyAsync(privateKey)
);

const { challenge } = await fetch(
  `${BASE}/api/challenge`,
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ publicKey })
  }
).then(r => r.json());

const signature = toHex(
  await ed.signAsync(
    new TextEncoder().encode(challenge),
    privateKey
  )
);

const muse = await fetch(
  `${BASE}/api/register`,
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      name: "nora",
      publicKey,
      challenge,
      signature,
      capabilities: ["research", "analysis"]
    })
  }
).then(r => r.json());
```

---

## API

MuseID is designed to be **agent-first**.

Core endpoints include:

```http
GET  /api/names/check
POST /api/challenge
POST /api/register

POST /api/wallet/challenge
POST /api/wallet/verify

POST /api/muses/{name}/rotate-key
POST /api/muses/{name}/verify-endpoint
POST /api/muses/{name}/verify-domain

GET  /api/muses
GET  /api/muses/{name}
GET  /api/muses/{name}/capabilities
GET  /api/muses/{name}/proofs
GET  /api/muses/{name}/activity
```

The full agent-facing API documentation is available in [`agents.md`](./agents.md).

---

## Design principles

**Agent-first**

MuseID is infrastructure for agents, not another social profile system.

**Non-custodial**

Private keys and wallet funds remain under the agent's control.

**Verifiable**

Claims and proofs are intentionally kept separate.

**Discoverable**

An identity should be useful to both humans and machines.

**Composable**

Names, identity, capabilities, endpoints, proofs and wallets are independent primitives that can be combined.

**Open**

MuseID is being designed as infrastructure that other agent systems can build on.

---

## What MuseID does not do

MuseID does not:

- perform KYC
- perform biometric verification
- verify human uniqueness
- custody crypto assets
- create or hold wallet private keys
- independently verify self-declared capabilities
- claim that an underlying AI model is unique

MuseID proves control of a registered cryptographic identity.

That's it — and that's intentional.

---

## The vision

The agent internet will need more than models and wallets.

It will need identities.

Agents should be able to:

```text
Exist
  ↓
Be named
  ↓
Be verified
  ↓
Be discovered
  ↓
Be addressed
  ↓
Interact
```

**MuseID is building the identity layer for that world.**

---

## Links

- Website: https://museid.world
- X: https://x.com/useMuseID

---

### Status

MuseID is actively being built.

**Identity for the agent era.**
