# AgentExec v2.6 ⚡
**The Decentralized x402 Payment & Compliance Gateway for Autonomous AI**
[![npm version](https://img.shields.io/npm/v/@ngtchain/mcp.svg)](https://www.npmjs.com/package/@ngtchain/mcp)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)]()
AgentExec is the infrastructure layer that enables autonomous AI agents to natively handle paywalled APIs, gasless execution, and complex regulatory compliance (like India VDA §194S TDS) without human intervention. By bridging the Model Context Protocol (MCP) with Base L2 settlement, AgentExec transforms LLMs from passive chatbots into active economic participants.
---
## 🏗️ Core Architecture
* **The AgentExec MCP Server:** An air-gapped, local context bridge exposing compliance and execution tools directly to your LLM.
* **HTTP 402 & EIP-3009:** Gasless, signature-based micro-payments settling instantly in USDC on Base.
* **VPCShield:** Built-in compliance module featuring append-only audit trails, normalized blocklists, and automated risk guardrails.
* **VeloSwitch:** Sub-50ms in-memory risk engine preventing transaction timeouts under high load.
---
## 🚀 The AgentExec MCP Server (Core Engine)
The AgentExec MCP Server is the heart of the stack. It runs locally alongside your AI agent, exposing tools like `screen_transaction` directly to the LLM's context window. This ensures compliance checks (VPCShield) and payment authorizations remain air-gapped and secure before any state is anchored to Base L2.
### 1. Installation & Setup
Clone the repository and install the dependencies:
```bash
git clone https://github.com/Har50/gateway.git
cd gateway
npm install
```
### 2. Environment Configuration
Create a `.env` file in the root directory and configure your Stage 2 Paid Mode facilitator key:
```env
# AgentExec Configuration
PORT=3100
AGENTEXEC_FACILITATOR_KEY=your_secure_facilitator_key_here
# Blockchain Configuration
BASE_RPC_URL=https://mainnet.base.org
USDC_ADDRESS=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913
```
### 3. Booting the Server
Start the local MCP server on port `3100`:
```bash
npm run start:mcp
```
*The server will boot at `http://127.0.0.1:3100/mcp`. Your local agents (Cursor, Claude, or custom bots like @blocksurfer) can now connect to this endpoint to access compliance and x402 settlement tools.*
---
## 📦 Modular Developer Tooling
If you are building custom workflows, AgentExec exposes its primitives via fully published open-source npm packages:
* [`@ngtchain/mcp` (v1.7.1)](https://www.npmjs.com/package/@ngtchain/mcp) - The core WebMCP compliance architecture.
* [`@ngtchain/client` (v1.5.2)](https://www.npmjs.com/package/@ngtchain/client) - Client-side libraries for autonomous agent signature generation.
* [`@ngtchain/paywall` (v0.2.1)](https://www.npmjs.com/package/@ngtchain/paywall) - Express/Fastify middleware for API gating via HTTP 402.
* [`@ngtchain/agentkit` (v0.1.1)](https://www.npmjs.com/package/@ngtchain/agentkit) - Streamlined orchestration for agentic multi-step payments.
### Example: Gating your API with `@ngtchain/paywall`
```typescript
import express from 'express';
import { x402TradingMiddleware } from '@ngtchain/paywall';
const app = express();
// Agent must pass an EIP-3009 signature in the header to access this endpoint
app.post('/api/v1/premium-data', x402TradingMiddleware, (req, res) => {
  res.json({ data: "Highly sensitive premium alpha." });
});
```
---
## 📈 HyperFluid DEX Integration
AgentExec v2.6 includes the foundational middleware for **HyperFluid**, a zero-gas matching engine.
* **Dual-Signature Asset Swaps:** Both Maker and Taker sign `receiveWithAuthorization` payloads.
* **Atomic Rollbacks:** Safe batching ensures a single failed signature does not revert an entire L2 settlement batch.
---
## 📄 License
MIT License. See `LICENSE` for details.
