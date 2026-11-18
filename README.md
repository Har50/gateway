# ⚡ AgentExec v2.6: Decentralized x402 & Compliance Gateway

[![Version](https://img.shields.io/badge/version-2.6-blue.svg)](https://github.com/Har50/gateway)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Network](https://img.shields.io/badge/network-Base_L2-blue)](https://base.org)
[![NPM Packages](https://img.shields.io/badge/npm-@ngtchain-red)](#packages)

**The infrastructure layer for autonomous AI agents to seamlessly handle paywalled APIs, gasless execution, and regulatory compliance.**

---

## 📖 Abstract

As autonomous AI agents evolve from conversational interfaces to active economic participants, they remain financially siloed by legacy banking infrastructure. Traditional payment gateways rely on human-centric identity verification (KYC), centralized clearinghouses, and cloud-dependent execution, creating latency bottlenecks and single points of failure. 

**AgentExec v2.6** solves this by establishing a decentralized, machine-native payment and compliance architecture on Base L2. By combining the HTTP 402 protocol, EIP-3009 gasless signatures, and an air-gapped sub-50ms risk engine, AgentExec enables autonomous systems to execute compliant, peer-to-peer micro-transactions natively.

---

## 🏗️ Core Architecture: The x402 Gateway

AgentExec revives and modernizes the native internet standard for micro-payments: the **HTTP 402 Payment Required** status code. Instead of routing traffic through third-party checkout processors, the AgentExec gateway bakes payment primitives directly into the API handshake.

1. **The Challenge:** An agent requests a premium endpoint. The server intercepts the request and responds with a `402` challenge, specifying the required fee (e.g., `0.001 USDC`) and the EVM settlement network (Base).
2. **The Signature:** The agent automatically generates an EIP-712 structured data signature utilizing its local private key, entirely off-chain.
3. **The Execution:** The server validates the signature in-memory. If valid, the API serves the data, and the signature is queued for batch settlement on the decentralized ledger.

---

## 🔗 Settlement Layer: Base L2 & EIP-3009

To eliminate the friction of agent wallets requiring native gas tokens (ETH), AgentExec leverages the **EIP-3009 (`transferWithAuthorization`)** standard for USDC on Base L2.

* **Zero-Gas Execution:** Agents never pay network gas fees. They solely sign cryptographic authorizations allowing the gateway to pull the exact USDC fee.
* **Batch Relaying:** To optimize throughput and prevent nonce-collision bottlenecks, approved x402 signatures are held in a PostgreSQL queue (`PENDING_ANCHOR`) and scooped by a background worker. The worker submits these as a unified multicall batch to a custom Base L2 smart contract, achieving fraction-of-a-cent settlement efficiency.
* **Cryptographic Finality:** By settling on Base, AgentExec achieves ~2-second finality on a decentralized, permissionless ledger, completely bypassing legacy clearinghouses.

---

## 🛡️ Compliance & Risk: VPCShield & VeloSwitch

In enterprise and institutional environments, executing decentralized transactions without localized compliance guardrails is a non-starter. AgentExec natively integrates a two-tier risk and compliance architecture designed for maximum throughput.

### VeloSwitch: Sub-50ms Risk Engine
Written in Node.js/Go, VeloSwitch is a high-performance in-memory payment risk engine. Utilizing V8 garbage collection tuning, asynchronous Pino logging, and LRU caching for static blocklists, VeloSwitch evaluates transaction velocity, payload schemas, and merchant codes in under 50 milliseconds, ensuring the gateway can handle 500+ RPS without blocking the event loop.

### VPCShield: Air-Gapped Regulatory Compliance
Unlike cloud-dependent KYC APIs, VPCShield operates entirely air-gapped within the enterprise's own infrastructure. 
* **India VDA §194S TDS Support:** Automatically calculates and verifies Tax Deducted at Source (TDS) requirements for Virtual Digital Assets prior to cryptographic execution.
* **Append-Only Audit Trails:** Every authorization decision is written asynchronously to an immutable database, ensuring enterprise-grade auditability without sacrificing execution speed.

---

## 🌊 HyperFluid DEX Integration

AgentExec extends its x402 primitives into decentralized trading via the **HyperFluid DEX**, a zero-cost settlement engine operating as a high-frequency, in-memory matching engine.

* **Dual-Signature Asset Swaps:** HyperFluid utilizes the `receiveWithAuthorization` function. Both the Maker and Taker sign gasless authorizations. When the engine matches the order off-chain, it batches the dual signatures and submits them to the `HyperFluidSettlement` smart contract.
* **Atomic Rollbacks:** Utilizing `try/catch` logic at the smart contract level, if one signature in a batch of 50 fails (e.g., the Maker canceled their nonce), the contract gracefully skips it and settles the remaining 49 trades, preventing total batch reversion.
* **No Vault Risk:** Funds remain in the users' or agents' personal wallets until the exact millisecond the trade is matched and executed, eliminating smart contract honeypot vulnerabilities.

---

## 📦 WebMCP & Developer Tooling

AgentExec is designed for immediate developer adoption via a modular, open-source stack. The system supports the Model Context Protocol (WebMCP), allowing local LLMs (such as Ollama/Qwen) and autonomous bots (like `@blocksurfer`) to ingest API logic dynamically.

### Official NPM Packages

| Package | Version | Description |
| :--- | :--- | :--- |
| [`@ngtchain/mcp`](https://www.npmjs.com/) | `v1.1.0` | The core WebMCP compliance architecture. |
| [`@ngtchain/client`](https://www.npmjs.com/) | `v1.1.0` | Client-side libraries for agent signature generation. |
| [`@ngtchain/paywall`](https://www.npmjs.com/) | `v0.1.0` | Express/Fastify middleware for API gating. |

**Registry Listing:** `io.github.Har50/gateway`

---

## 🚀 Quick Start

To gate your existing Express/Fastify API with the AgentExec x402 paywall:

```bash
npm install @ngtchain/paywall @ngtchain/client
```

```typescript
import { x402TradingMiddleware } from '@ngtchain/paywall';
import express from 'express';

const app = express();

// Protect your endpoint with a 0.001 USDC fee
app.post('/api/premium-data', x402TradingMiddleware({ fee: 0.001, currency: 'USDC' }), (req, res) => {
  res.json({ data: "Highly valuable, agent-accessible data." });
});

app.listen(4000);
```

---
*Built for a resilient, agent-driven economy on Base.*