---
name: chainaware-behavioral-prediction
description: >
  Use this skill whenever a user asks about wallet safety, fraud risk, rug pull detection,
  wallet behavior analysis, DeFi personalization, on-chain reputation scoring, or token ranking
  by holder quality. Triggers on questions like "is this wallet safe?", "will this pool rug pull?",
  "what will this address do next?", "score this wallet", "detect fraud for address",
  "personalize my DeFi agent", "rank this token", "top AI tokens", "best holders of this token",
  or any request to analyze a blockchain wallet address, smart contract, or token for risk,
  behavior, intent, or community strength. Also use when integrating the ChainAware MCP server
  into Claude Code, Cursor, ChatGPT, or any MCP-compatible AI agent framework.
version: 1.0.0
metadata:
  openclaw:
    requires:
      env: [CHAINAWARE_API_KEY]
    primaryEnv: CHAINAWARE_API_KEY
    emoji: "🔮"
    homepage: https://github.com/ChainAware/behavioral-prediction-mcp
---

# ChainAware Behavioral Prediction MCP

## What This Skill Does

The **ChainAware Behavioral Prediction MCP** connects any AI agent to a continuously updated
Web3 behavioral intelligence layer: **14M+ wallet profiles** across **8 blockchains**, built from
**1.3 billion+ predictive data points**. It delivers three core capabilities via a single MCP
endpoint:

1. **Fraud Detection** — predict fraudulent wallet behavior before it happens (~98% accuracy on ETH)
2. **Behavioral Analysis** — profile wallet intent, risk tolerance, experience, and next likely actions
3. **Rug Pull Detection** — forecast whether a smart contract or liquidity pool will rug pull
4. **Token Ranking** — rank tokens by holder community strength and surface top holders with wallet-level intelligence

Unlike forensic blockchain tools that describe the past, this MCP is **predictive** — it tells your
agent what is *about to happen*.

**MCP Server URL:** `https://prediction.mcp.chainaware.ai/sse`
**GitHub:** https://github.com/ChainAware/behavioral-prediction-mcp
**Website:** https://chainaware.ai
**Pricing / API Key:** https://chainaware.ai/pricing

---

## Supported Blockchains

| Tool                  | Networks                                          |
|-----------------------|---------------------------------------------------|
| Fraud Detection       | ETH, BNB, POLYGON, TON, BASE, TRON, HAQQ         |
| Behavioral Analysis   | ETH, BNB, BASE, HAQQ, SOLANA                     |
| Rug Pull Detection    | ETH, BNB, BASE, HAQQ                             |
| Token Rank List       | ETH, BNB, BASE, SOLANA                            |
| Token Rank Single     | ETH, BNB, BASE, SOLANA                            |

---

## Available Tools

### 1. `predictive_fraud` — Fraud Detection

Forecasts the probability that a wallet will engage in fraudulent activity. Includes AML checks.
Use when a user wants to screen a wallet before interacting with it.

**Inputs:**
- `apiKey` (string, required) — ChainAware API key
- `network` (string, required) — e.g. `ETH`, `BNB`, `BASE`
- `walletAddress` (string, required) — the wallet to evaluate

**Key output fields:**
- `status` — `"Fraud"`, `"Not Fraud"`, or `"New Address"`
- `probabilityFraud` — decimal 0.00–1.00
- `forensic_details` — deep on-chain breakdown

**Example prompts that trigger this tool:**
- *"Is it safe to interact with vitalik.eth?"*
- *"What is the fraud risk of 0xABC... on Ethereum?"*
- *"Run an AML check on this BNB wallet: 0x123..."*
- *"Is my new wallet at risk of being flagged for fraud?"*

---

### 2. `predictive_behaviour` — Behavioral Analysis & Personalization

Profiles a wallet's on-chain history and predicts its next actions. Use for DeFi personalization,
user segmentation, next-best-action recommendations, and experience-level scoring.

**Inputs:**
- `apiKey` (string, required)
- `network` (string, required) — e.g. `ETH`, `SOLANA`
- `walletAddress` (string, required)

**Key output fields:**
- `intention` — predicted next actions (e.g. `Prob_Trade: "High"`, `Prob_Stake: "Medium"`)
- `recommendation` — personalized action suggestions based on behavioral history
- `categories` — behavioral segments (DeFi Lender, NFT Trader, Bridge User, etc.)
- `riskProfile` — risk tolerance and balance age breakdown
- `experience` — experience score (beginner → expert)
- `protocols` — which protocols this wallet uses (Aave, Uniswap, GMX, etc.)
- `status` + `probabilityFraud` — fraud context included in every response

**Example prompts that trigger this tool:**
- *"What will 0xABC... on ETH do next?"*
- *"Is this user a DeFi lender or an NFT trader?"*
- *"Recommend the best yield strategy for 0x123... on BASE."*
- *"What's the experience level of this wallet?"*
- *"Personalize my DeFi agent's response for this wallet address."*

---

### 3. `predictive_rug_pull` — Rug Pull Detection

Forecasts whether a smart contract or liquidity pool is likely to execute a rug pull. Use to
protect users before they deposit capital into risky contracts.

**Inputs:**
- `apiKey` (string, required)
- `network` (string, required) — e.g. `ETH`, `BNB`
- `walletAddress` (string, required) — smart contract or LP address to evaluate

**Key output fields:**
- `status` — `"Fraud"` or `"Not Fraud"`
- `probabilityFraud` — decimal 0.00–1.00
- `forensic_details` — on-chain metrics behind the score

**Example prompts that trigger this tool:**
- *"Will this new DeFi pool rug pull if I stake my assets?"*
- *"Is this smart contract safe? Address: 0x456... on BNB."*
- *"Monitor this LP position for rug pull risk."*
- *"Check if this launchpad project is legitimate before I invest."*

---

### 4. `token_rank_list` — Token Ranking by Holder Strength

Ranks tokens by the quality and strength of their holder community. Use when a user wants to
discover, compare, or filter tokens across chains and categories based on holder behavior — not
just market cap or price.

**Inputs:**
- `limit` (string, required) — Number of items to return per page
- `offset` (string, required) — Page number for pagination
- `network` (string, required) — e.g. `ETH`, `BNB`, `BASE`, `SOLANA`
- `sort_by` (string, required) — Field to sort by, e.g. `communityRank`
- `sort_order` (string, required) — `ASC` or `DESC`
- `category` (string, required) — Token category filter: `AI Token`, `RWA Token`, `DeFi Token`, `DeFAI Token`, `DePIN Token`
- `contract_name` (string, required) — Search by contract/token name (use empty string for no filter)

**Key output fields:**
- `data.total` — total number of matching tokens
- `data.contracts[]` — array of token objects, each with:
  - `contractAddress`, `contractName`, `ticker`, `chain`
  - `category` — token category label
  - `communityRank` — raw ranking based on community holder metrics
  - `normalizedRank` — normalized/scaled ranking score
  - `totalHolders` — total unique wallet holders

**Example prompts that trigger this tool:**
- *"What are the top AI tokens on Ethereum?"*
- *"Rank DeFi tokens on BNB by community strength."*
- *"Which RWA tokens have the strongest holder base on BASE?"*
- *"Compare DePIN tokens across Solana and Ethereum."*
- *"Show me the top 10 tokens by community rank on ETH."*

---

### 5. `token_rank_single` — Single Token Rank & Top Holders

Returns the rank and top holders for a specific token by contract address. Use when a user wants
to deep-dive into a single token's community quality, see who the strongest holders are, and
understand the token's global standing.

**Inputs:**
- `contract_address` (string, required) — The token contract or mint address
- `network` (string, required) — e.g. `ETH`, `BNB`, `BASE`, `SOLANA`

**Key output fields:**
- `data.contract` — token details including:
  - `contractName`, `ticker`, `chain`, `category`
  - `communityRank` — raw community-based ranking
  - `normalizedRank` — normalized ranking score
  - `totalHolders` — total unique holders
- `data.topHolders[]` — array of top holder objects, each with:
  - `Holder.walletAddress` — holder's wallet address
  - `Holder.balance` — token balance held
  - `Holder.walletAgeInDays` — wallet age in days
  - `Holder.transactionsNumber` — total transaction count
  - `Holder.totalPoints` — computed wallet scoring metric
  - `Holder.globalRank` — wallet rank across the entire ChainAware network

**Example prompts that trigger this tool:**
- *"What is the token rank for USDT on Ethereum?"*
- *"Who are the top holders of this token: 0xdAC17F958D2ee523a2206206994597C13D831ec7 on ETH?"*
- *"Show me the best holders and community rank for this Solana token."*
- *"How strong is the holder base of this contract on BNB?"*
- *"What's the global rank of wallets holding this BASE token?"*

---

## Integration Setup

### Claude Code (CLI)
```bash
claude mcp add --transport sse chainaware-behavioural-prediction-mcp-server \
  https://prediction.mcp.chainaware.ai/sse \
  --header "X-API-Key: your-key-here"
```
📚 Docs: https://code.claude.com/docs/en/mcp

### Claude Web / Claude Desktop
1. Go to **Settings → Integrations → Add integration**
2. Name: `ChainAware Behavioural Prediction MCP Server`
3. URL: `https://prediction.mcp.chainaware.ai/sse?apiKey=your-key-here`

📚 Docs: https://platform.claude.com/docs/en/agents-and-tools/remote-mcp-servers

### Cursor (`mcp.json`)
```json
{
  "mcpServers": {
    "chainaware-behavioural-prediction-mcp-server": {
      "url": "https://prediction.mcp.chainaware.ai/sse",
      "transport": "sse",
      "headers": {
        "X-API-Key": "your-key-here"
      }
    }
  }
}
```
📚 Docs: https://cursor.com/docs/context/mcp

### ChatGPT Connectors
1. Open **ChatGPT Settings → Apps / Connectors → Add Connector**
2. Name: `ChainAware Behavioural Prediction MCP Server`
3. URL: `https://prediction.mcp.chainaware.ai/sse?apiKey=your-key-here`

### Node.js
```javascript
import { MCPClient } from "mcp-client";

const client = new MCPClient("https://prediction.mcp.chainaware.ai/");

// Fraud check
const fraud = await client.call("predictive_fraud", {
  apiKey: process.env.CHAINAWARE_API_KEY,
  network: "ETH",
  walletAddress: "0xYourWalletAddress"
});

// Behavior profile
const behavior = await client.call("predictive_behaviour", {
  apiKey: process.env.CHAINAWARE_API_KEY,
  network: "ETH",
  walletAddress: "0xYourWalletAddress"
});

// Rug pull risk
const rugPull = await client.call("predictive_rug_pull", {
  apiKey: process.env.CHAINAWARE_API_KEY,
  network: "BNB",
  walletAddress: "0xContractAddress"
});

// Token rank list
const topTokens = await client.call("token_rank_list", {
  limit: "10",
  offset: "0",
  network: "ETH",
  sort_by: "communityRank",
  sort_order: "DESC",
  category: "AI Token",
  contract_name: ""
});

// Single token rank + top holders
const tokenDetail = await client.call("token_rank_single", {
  contract_address: "0xdAC17F958D2ee523a2206206994597C13D831ec7",
  network: "ETH"
});
```

### Python
```python
from mcp_client import MCPClient
import os

client = MCPClient("https://prediction.mcp.chainaware.ai/")

result = client.call("predictive_fraud", {
    "apiKey": os.environ["CHAINAWARE_API_KEY"],
    "network": "ETH",
    "walletAddress": "0xYourWalletAddress"
})
print(result)

# Token rank list
top_tokens = client.call("token_rank_list", {
    "limit": "10",
    "offset": "0",
    "network": "ETH",
    "sort_by": "communityRank",
    "sort_order": "DESC",
    "category": "AI Token",
    "contract_name": ""
})
print(top_tokens)

# Single token rank + top holders
token_detail = client.call("token_rank_single", {
    "contract_address": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
    "network": "ETH"
})
print(token_detail)
```

---

## Real-World Use Cases

### DeFi Platforms
- **Liquidity management** — use `add_liquidity_probability` and `withdraw_probability` scores
  to pre-position reserves and prevent pool drain before it happens
- **Yield routing** — identify wallets with high yield-seeking intent and route them to optimal
  vaults before competitors do
- **Risk-adjusted lending** — use fraud scores and behavioral profiles to set collateral
  requirements and interest rates per borrower

### AI Agent Personalization
- Give your agent a real-time behavioral profile of each wallet it talks to — so it stops
  serving generic prompts and starts delivering relevant, contextual responses
- Segment users automatically into DeFi Lender, NFT Trader, Bridge User, New Wallet, etc.
  and customize your agent's UX per segment

### Fraud & Compliance
- Screen wallets at the point of entry to your Dapp — before any transaction takes place
- Run continuous AML monitoring across all active wallets, not just one-time checks
- Detect rug pull contracts at launchpad listing stage, before community capital is at risk

### NFT & GameFi
- Personalize in-game economies and item recommendations based on a player wallet's
  on-chain history and spending patterns
- Filter bot wallets and wash traders from NFT drops using fraud scores

---

## Background Reading

These articles provide deep context on the MCP's architecture, use cases, and business impact:

| Article | URL |
|---------|-----|
| Complete ChainAware Product Guide | https://chainaware.ai/blog/chainaware-ai-products-complete-guide/ |
| Prediction MCP: Complete Developer Guide | https://chainaware.ai/blog/prediction-mcp-for-ai-agents-personalize-decisions-from-wallet-behavior/ |
| Why Personalization Is the Next Big Thing for AI Agents | https://chainaware.ai/blog/why-personalization-is-the-next-big-thing-for-ai-agents/ |
| Top 5 Ways Prediction MCP Will Turbocharge Your DeFi Platform | https://chainaware.ai/blog/top-5-ways-prediction-mcp-will-turbocharge-your-defi-platform/ |

---

## Security Notes

- **Never hard-code API keys** in public repositories
- Use environment variables or secret managers in production
- Rotate API keys regularly
- The server uses **SSE (Server-Sent Events)** for streaming responses
- Rate limits apply depending on your subscription tier

---

## Error Reference

| Code | Meaning |
|------|---------|
| `403 Unauthorized` | Invalid or missing `apiKey` |
| `400 Bad Request` | Malformed `network` or `walletAddress` |
| `500 Internal Server Error` | Temporary backend failure — retry after a short delay |

---

## Access & Pricing

API key required. Subscribe at: https://chainaware.ai/pricing
