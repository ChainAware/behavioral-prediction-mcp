# 🧠 ChainAware Behavioural Prediction MCP Server

**MCP Server Name:** ChainAware Behavioural Prediction MCP

**Category:** Web3 / Security / DeFi Analytics

**Status:** Public tools – Private backend

**Access:** By request (API key)

**Server URL:** [https://prediction.mcp.chainaware.ai/sse]

**Repository:** [https://github.com/ChainAware/behavioral-prediction-mcp]

**Website:** [https://chainaware.ai/]

**Twitter:** [https://x.com/ChainAware/]

<!-- MCP -->
mcp-name: io.github.ChainAware/chainaware-behavioral-prediction-mcp

---

## 📖 Description

The **Behavioural Prediction MCP Server** provides AI-powered tools to analyze wallet behaviour prediction,fraud detection and rug pull prediction.

Developers and platforms can integrate these tools through the MCP protocol to safeguard DeFi users, monitor liquidity risks, and score wallet or contract trustworthiness.

All tools follow the [Model Context Protocol (MCP)](https://github.com/modelcontextprotocol/spec) and can be consumed via MCP-compatible clients.

---

## ⚙️ Available Tools

### 1. Predictive Fraud Detection Tool

**ID:** `predictive_fraud`

**Description:**
This AI‑powered algorithm forecasts the likelihood of fraudulent activity on a given wallet address *before* it happens (≈98% accuracy), and performs AML/Anti‑Money‑Laundering checks. 
Use this when your user wants a risk assessment or early‑warning on a blockchain address.

➡️ Example Use Cases:

    • Is it safe to intercant with vitalik.eth ?
    • What is the fraudulent status of this address ?
    • Is my new wallet at risk of being used for fraud?  

**Inputs:**

| Name            | Type   | Required | Description                                                               |
| --------------- | ------ | -------- | ------------------------------------------------------------------------- |
| `apiKey`        | string | ✅        | API key for authentication                                               |
| `network`       | string | ✅        | Blockchain network (`ETH`, `BNB`,`POLYGON`,`TON`,`BASE`, `TRON`, `HAQQ`) |
| `walletAddress` | string | ✅        | The wallet address to evaluate                                           |



**Outputs (JSON):**

```json
{
    "message": "string",              // Human‑readable status message
    "walletAddress": "string",        // hex address 
    "status": "Fraud",                // Fraudelent status (Fraud,Not Fraud,New Address)
    "probabilityFraud": "0.00–1.00",  // Decimal probability
    "token": "string",                //
    "lastChecked": "ISO‑8601 timestamp",
    "forensic_details": {             // Deep forensic breakdown
    /* ...other metrics... */
    },
    "createdAt": "ISO‑8601 timestamp", 
    "updatedAt": "ISO‑8601 timestamp"
}
```

Error cases:

    • `403 Unauthorized` → invalid `apiKey`  
    • `400 Bad Request` → malformed `network` or `walletAddress`  
    • `500 Internal Server Error` → temporary downstream failure  
---

### 2. Predictive Behaviour Analysis Tool

**ID:** `predictive_behaviour`

**Description:**
    This AI‑driven engine projects what a wallet address intentions or what address is likely to do next,
    profiles its past on‑chain history, and recommends personalized actions.

    Use this when you need:

      • Next‑best‑action predictions and intentions(“Will this address deposit, trade, or stake?”)  
      • A risk‑tolerance and experience profile  
      • Category segmentation (e.g. NFT, DeFi, Bridge usage)  
      • Custom recommendations based on historical patterns


➡️ Example Use Cases:

    • “What will this address do next?”  
    • “Is the user high‑risk or experienced?”  
    • “Recommend the best DeFi strategies for 0x1234... on ETH network.”

**Inputs:**

| Name            | Type   | Required           | Description                                                               |
| --------------- | ------ | ------------------ | ------------------------------------------------------------------------- |
| `apiKey`        | string | ✅                 | API key for authentication                                                |
| `network`       | string | ✅                 | Blockchain network (`ETH`, `BNB`,`BASE`,`HAQQ`,`SOLANA`)                  |
| `walletAddress` | string | ✅                 | The wallet address to evaluate                                            |



**Outputs (JSON):**

```json
{
    "message":           "string",                    // e.g. “Success” or error text  
    "walletAddress":     "string",                    // echoed input  
    "status":            "string",                    // Fraudelent status (Fraud,Not Fraud,New Address)  
    "probabilityFraud":  "0.00–1.00",                 // decimal fraud score  
    "lastChecked":       "ISO‑8601 timestamp",        // e.g. “2025‑01‑03T16:19:13.000Z”  
    "forensic_details":  { /* dict of forensic metrics */ },  
    "categories":        [ { "Category":"string", "Count":int }, … ],  
    "riskProfile":       [ { "Category":"string", "Balance_age":float }, … ],  
    "segmentInfo":       "JSON‑string of segment counts",  
    "experience":        { "Type":"Experience", "Value":int },  
    "intention":         {                              
    "Type":"Intentions",  
    "Value": { "Prob_Trade":"High", "Prob_Stake":"Medium", … }  
    },  
    "protocols":         [ { "Protocol":"string","Count":int }, … ],  
    "recommendation":    { "Type":"Recommendation", "Value":[ "string", … ] },  
    "createdAt":         "ISO‑8601 timestamp",  
    "updatedAt":         "ISO‑8601 timestamp"  
}
```

Error cases:

    • `403 Unauthorized` → invalid `apiKey`  
    • `400 Bad Request` → malformed `network` or `walletAddress`  
    • `500 Internal Server Error` → temporary downstream failure  
---


### 3. Predictive Rug‑Pull Detection Tool

**ID:** `predictive_rug_pull`

**Description:**
This AI‑powered engine forecasts which liquidity pools or contracts are likely to perform a “rug pull” in the future. Use this when you need to warn users before they deposit into risky pools or to monitor smart‑contract security on-chain.

➡️ Example Use Cases:

    • “Will this new DeFi pool rug‑pull if I stake my assets?”  
    • “Monitor my LP position for potential future exploits.”  

**Inputs:**

| Name            | Type   | Required | Description                                              |
| --------------- | ------ | -------- | -------------------------------------------------------  |
| `apiKey`        | string | ✅        | API key for authentication                              |
| `network`       | string | ✅        | Blockchain network (`ETH`, `BNB`, `BASE`, `HAQQ`)       |
| `walletAddress` | string | ✅        | Smart contract or liquidity pool address                |

**Outputs (JSON):**

```json
{
  "message": "Success",
  "contractAddress": "0x1234...",
  "status": "Fraud",
  "probabilityFraud": 0.87,
  "lastChecked": "2025-10-25T12:45:00Z",
  "forensic_details": { /* dict of on‑chain metrics */ }, 
  "createdAt": "2025-10-25T12:45:00Z",
  "updatedAt": "2025-10-25T12:45:00Z"
}
```

Error cases:

    • `403 Unauthorized` → invalid `apiKey`  
    • `400 Bad Request` → malformed `network` or `walletAddress`  
    • `500 Internal Server Error` → temporary downstream failure  

---


### 4. Token Rank List Tool

**ID:** `token_rank_list`

**Description:**
TokenRank analyzes the community of token holders and ranks every token by the strength of its holders. The stronger the token holders, the stronger the token! 
Use this when you need to know token rank of a token or tokens or compare between different categories and chains.
You can use search,filter and sort and pagination which returns a list of tokens.

➡️ Example Use Cases:
  – “Which is the best token on AI Token category?”  
  – “Compare x token in ETH chain and BNB chain?”  

**Inputs:**

| Name            | Type   | Required | Description                                                                                                     |
| --------------- | ------ | -------- | --------------------------------------------------------------------------------------------------------------  |
| `limit`         | string | ✅       | Number of items ot fetch during pagination                                                                      |
| `offset`        | string | ✅       | Page number(offset) during pagination                                                                           |
| `network`       | string |          | Blockchain network to filter (`ETH`, `BNB`, `BASE`, `SOLANA`)                                                   |
| `sort_by`       | string |          | Sort the returnet tokens based on (e.g.: 'communityRank')                                                       |
| `sort_order`    | string |          | 'ASC' or 'DESC' sorting the value of sort_by                                                                    |
| `category`      | string |          | Filter based on category of the token (e.g. 'AI Token','RWA Token','DeFi Token','DeFAI Token','DePIN Token')    |
| `contract_name` | string |          | Search based on contract name                                                                                   |


**Outputs (JSON):**

```json
  {
    "message": "string",                    // e.g. “Successfully fetched records” or error description
    "data": {
      "total": 0,                           // integer — total number of matching contracts
      "contracts": [
        {
          "contractAddress": "string",       // unique contract or mint address (chain-specific format)
          "contractName": "string",          // human-readable token name
          "ticker": "string",                // token symbol (usually uppercase, but not guaranteed)
          "chain": "string",                 // blockchain network (e.g. SOLANA | ETH | BNB | BASE)
          "category": "string",              // primary category label (e.g. 'AI Token','RWA Token','DeFi Token','DeFAI Token','DePIN Token') 
          "type": "string",                  // asset classification (e.g. “token” | “nft”)
          "communityRank": 0,                // integer — raw ranking based on community metrics
          "normalizedRank": 0,               // integer — normalized or scaled ranking score
          "totalHolders": 0,                 // integer — total unique wallet holders
          "lastProcessedAt": "ISO-8601",     // timestamp when analytics were last computed
          "createdAt": "ISO-8601",           // record creation timestamp
          "updatedAt": "ISO-8601"            // record last update timestamp
        }
      ]
    }
  }
```

Error cases:

    • `400 Bad Request` → malformed `network` or `walletAddress`  
    • `500 Internal Server Error` → temporary downstream failure  

---

### 5. Token Rank Single Tool

**ID:** `token_rank_single`

**Description:**
  Similar to TokenRank List,Token Rank analyzes the community of token holders and ranks every token by the strength of its holders.
  Except the token rank and token details the token rank single tool fetches the best holders their details and its globalRank alongside others in same network.
  Use this when you need to know token rank of a single token based on contract address and exeact chain or network or when you need best holders of specific token in specifc network or chain

➡️ Example Use Cases:
  – “What is the token rank for token in ETH network?”  
  – "Which are the best holders of this contract token address?”
  – “What is the token rank and its best holders?”

**Inputs:**

| Name              | Type   | Required  | Description                                                    |
| ------------------| ------ | --------- | -------------------------------------------------------------  |
| `contract_address`| string | ✅        | The contract address of the token to evaluate                  |
| `network`         | string | ✅        | Blockchain network to filter (`ETH`, `BNB`, `BASE`, `SOLANA`)  |


**Outputs (JSON):**

```json
 {
      "message": "string",                      // e.g. “Successfully fetched records” or error description
      "data": {
        "contract": {
            "contractAddress": "string",       // unique contract or mint address (chain-specific format)
            "contractName": "string",          // human-readable token name
            "ticker": "string",                // token symbol (usually uppercase, but not guaranteed)
            "chain": "string",                 // blockchain network (e.g. SOLANA | ETH | BNB | BASE)
            "category": "string",              // primary category label (e.g. 'AI Token','RWA Token','DeFi Token','DeFAI Token','DePIN Token') 
            "type": "string",                  // asset classification (e.g. “token” | “nft”)
            "communityRank": 0,                // integer — raw ranking based on community metrics
            "normalizedRank": 0,               // integer — normalized or scaled ranking score
            "totalHolders": 0,                 // integer — total unique wallet holders
            "lastProcessedAt": "ISO-8601",     // timestamp when analytics were last computed
            "createdAt": "ISO-8601",           // record creation timestamp
            "updatedAt": "ISO-8601"            // record last update timestamp
        },
        "topHolders": [
          {
            "contractAddress": "string",        // associated contract address
            "Holder": {
              "walletAddress": "string",        // holder wallet address
              "chain": "string",                // blockchain network of the wallet
              "balance": "string",              // token balance (string to preserve precision)
              "walletAgeInDays": 0,             // integer — age of wallet in days
              "transactionsNumber": 0,          // integer — total transaction count
              "totalPoints": 0.0,               // float — computed wallet scoring metric
              "globalRank": 0                   // integer — wallet rank across entire system
            }
          }
        ]
      }
    }
```

Error cases:

    • `400 Bad Request` → malformed `network` or `walletAddress`  
    • `500 Internal Server Error` → temporary downstream failure  

---

## 🧠 Example Client Usage

### Node.js Example

```js
import { MCPClient } from "mcp-client";

const client = new MCPClient("https://prediction.mcp.chainaware.ai/");

const result = await client.call("predictive_rug_pull", {
  apiKey: "your_api_key",
  network: "BNB",
  walletAddress: "0x1234..."
});

console.log(result);
```

### Python Example

```python
from mcp_client import MCPClient

client = MCPClient("https://prediction.mcp.chainaware.ai/")

res = client.call("chat", {"query": "What is the rug pull risk of 0x1234?"})
print(res)
```

---

Service Configuration:
```{
  "type": "sse",
  "config": {
    "mcpServers": {
      "chainaware-behavioural_prediction_mcp": {
        "type": "sse",
        "url": "https://prediction.mcp.chainaware.ai/sse",
        "description": "The Behavioural Prediction MCP Server provides AI-powered tools to analyze wallet behaviour prediction,fraud detection and rug pull prediction.",
        "headers":{
          "x-api-key":""
        },
        "params":{
          "walletAddress":"",
          "network":""
        },
        "auth": {
          "type": "api_key",
          "header": "X-API-Key"
        }
      }
    }
  }
}
```
---

## 🔌 Integration Notes

* ✅ Compatible with MCP clients across **Node.js**, **Python**, and **browser-based** environments
* 🔁 Uses **Server-Sent Events (SSE)** for streaming / real-time responses
* 📐 JSON schemas conform to the **MCP specification**
* 🚦 Rate limits may apply depending on usage tier
* 🔑 **API key required** for production endpoints

---

## Claude Code (CLI) Configuration

Use the Claude CLI to register the MCP server via SSE transport:

```bash
claude mcp add --transport sse chainaware-behavioural-prediction-mcp-server https://prediction.mcp.chainaware.ai/sse \
  --header "X-API-Key: your-key-here"
```

📚 Documentation:
[https://code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)

---

## ChatGPT Connector Configuration

> Available in ChatGPT environments that support **Connectors / MCP** (Developer Mode).

### Steps

1. Open **ChatGPT Settings**
2. Navigate to **Apps / Connectors**
3. Click **Add Connector**
4. Enter the integration name and URL below
5. Save the configuration

### Integration Details

**Name**

```
ChainAware Behavioural Prediction MCP Server
```

**Integration URL**

```
https://prediction.mcp.chainaware.ai/sse?apiKey=your-key-here
```

---

## Claude Web & Claude Desktop Configuration

### Steps

1. Open **Claude Web** or **Claude Desktop**
2. Go to **Settings → Integrations**
3. Click **Add integration**
4. Enter the name and URL below
5. Click **Add** to complete setup

### Integration Details

**Name**

```
ChainAware Behavioural Prediction MCP Server
```

**Integration URL**

```
https://prediction.mcp.chainaware.ai/sse?apiKey=your-key-here
```

📚 Documentation:
[https://platform.claude.com/docs/en/agents-and-tools/remote-mcp-servers](https://platform.claude.com/docs/en/agents-and-tools/remote-mcp-servers)

---

## Cursor Configuration

Add the MCP server to your Cursor configuration file (e.g. `mcp.json`):

```json
{
  "mcpServers": {
    "chainaware-behavioural-prediction-mcp-server": {
      "url": "https://prediction.mcpbeta.chainaware.ai/sse",
      "transport": "sse",
      "headers": {
        "X-API-Key": "your-key-here"
      }
    }
  }
}
```

📚 Documentation:
[https://cursor.com/docs/context/mcp](https://cursor.com/docs/context/mcp)

---

## 🤖 Claude Code Subagents

This repository includes **22 ready-to-use Claude Code subagents** in `.claude/agents/` — specialist agents that handle common Web3 intelligence tasks out of the box.

| Agent | Purpose |
|-------|---------|
| `chainaware-analyst` | Full due diligence — fraud + behavior + rug pull |
| `chainaware-fraud-detector` | Fast wallet fraud screening |
| `chainaware-rug-pull-detector` | Smart contract / LP safety checks |
| `chainaware-trust-scorer` | Trust score (0.00–1.00) |
| `chainaware-reputation-scorer` | Reputation score (0–4000) |
| `chainaware-aml-scorer` | AML compliance scoring (0–100) |
| `chainaware-wallet-ranker` | Wallet experience rank + leaderboard |
| `chainaware-wallet-marketer` | Personalized marketing messages |
| `chainaware-token-ranker` | Discover/rank tokens by holder community strength |
| `chainaware-token-analyzer` | Single token deep-dive + top holders |
| `chainaware-onboarding-router` | Route wallets to beginner/intermediate/skip onboarding |
| `chainaware-whale-detector` | Classify wallets into whale tiers (Mega/Whale/Emerging) |
| `chainaware-defi-advisor` | Personalized DeFi product recommendations by experience + risk tier |
| `chainaware-airdrop-screener` | Batch screen wallets for airdrop eligibility, filter bots/fraud |
| `chainaware-lending-risk-assessor` | Borrower risk grade (A–F), collateral ratio, interest rate tier |
| `chainaware-token-launch-auditor` | Pre-listing launch safety audit — APPROVED/CONDITIONAL/REJECTED |
| `chainaware-agent-screener` | AI agent trust score 0–10 via agent + feeder wallet checks |
| `chainaware-cohort-analyzer` | Segment a batch of wallets into behavioral cohorts with per-cohort engagement strategies |
| `chainaware-counterparty-screener` | Real-time pre-transaction go/no-go verdict (Safe / Caution / Block) before a trade, transfer, or contract interaction |
| `chainaware-governance-screener` | DAO voter screening — Sybil detection, governance tier, and voting weight multiplier (supports token-weighted, reputation-weighted, and quadratic models) |
| `chainaware-transaction-monitor` | Real-time transaction risk scoring for autonomous agents — composite score (0–100) and pipeline action (ALLOW / FLAG / HOLD / BLOCK) |
| `chainaware-lead-scorer` | Sales lead qualification — lead score (0–100), tier (Hot/Warm/Cold/Dead), conversion probability, and recommended outreach angle |

### Setup

**Step 1 — Connect the MCP server**

The agents call ChainAware tools via MCP. Register the server first:

```bash
claude mcp add --transport sse chainaware-behavioral-prediction \
  https://prediction.mcp.chainaware.ai/sse \
  --header "X-API-Key: YOUR_KEY"
```

For Cursor / Windsurf, add to `mcp.json`:

```json
{
  "mcpServers": {
    "chainaware-behavioral-prediction": {
      "url": "https://prediction.mcp.chainaware.ai/sse",
      "transport": "sse",
      "headers": { "X-API-Key": "YOUR_KEY" }
    }
  }
}
```

**Step 2 — Copy the agent files**

Clone this repo and copy the agents into your project:

```bash
git clone https://github.com/ChainAware/behavioral-prediction-mcp.git
cp -r behavioral-prediction-mcp/.claude/agents/ your-project/.claude/agents/
```

Or cherry-pick only the agents you need:

```bash
mkdir -p your-project/.claude/agents
cp behavioral-prediction-mcp/.claude/agents/chainaware-fraud-detector.md \
   your-project/.claude/agents/
```

**Step 3 — Set the API key**

```bash
export CHAINAWARE_API_KEY="your-key-here"
```

Get a key at [https://chainaware.ai/pricing](https://chainaware.ai/pricing)

### Important Notes

* The `tools:` line in each agent's frontmatter references the MCP server by its registered name. If you register the server under a different name, update the `tools:` lines to match.
* Agents specify a `model:` in their frontmatter (`claude-haiku-4-5-20251001` or `claude-sonnet-4-6`). You need access to those models.
* The `references/` folder contains detailed tool documentation that gives agents richer context. Copying it alongside the agents is recommended but optional.

---

## 🔐 Security Notes

* Do **not** hard-code API keys in public repositories
* Prefer environment variables or secret managers when supported
* Rotate keys regularly in production environments

---

## 🔒 Access Policy

The MCP server requires an API key for production usage.
To request access:

* You can subscribe to listed available plans via: https://chainaware.ai/pricing

---

## 📖 Further Reading

- [12 Blockchain Capabilities Any AI Agent Can Use — MCP Integration Guide](https://chainaware.ai/blog/12-blockchain-capabilities-any-ai-agent-can-use-mcp-integration-guide/) — All capabilities explained, with setup instructions for Claude, ChatGPT, Cursor, and multi-agent systems
- [Prediction MCP for AI Agents: Personalize Decisions from Wallet Behavior](https://chainaware.ai/blog/prediction-mcp-for-ai-agents-personalize-decisions-from-wallet-behavior/) — Deep-dive integration guide with code examples
- [Top 5 Ways Prediction MCP Will Turbocharge Your DeFi Platform](https://chainaware.ai/blog/top-5-ways-prediction-mcp-will-turbocharge-your-defi-platform/) — Lending, DEX, launchpad, governance, and personalization use cases
- [ChainAware Complete Product Guide](https://chainaware.ai/blog/chainaware-ai-products-complete-guide/) — Overview of all tools, networks, and coverage
- [Web3 Business Potential](https://chainaware.ai/blog/web3-business-potential/) — Business case and market opportunity for Web3 intelligence
- [Why Personalization Is the Next Big Thing for AI Agents](https://chainaware.ai/blog/why-personalization-is-the-next-big-thing-for-ai-agents/) — The case for wallet-level personalization in Web3
- [Use ChainAware as a Business](https://chainaware.ai/blog/use-chainaware-as-business/) — How to build commercial products and services on top of ChainAware
- [DeFi Onboarding in 2026: Why 90% of Connected Wallets Never Transact and How AI Agents Fix It](https://chainaware.ai/blog/defi-onboarding-in-2026-why-90-of-connected-wallets-never-transact-and-how-ai-agents-fix-it/) — Onboarding conversion problem and AI-driven solutions
- [The Web3 Agentic Economy: How AI Agents Are Replacing Human Teams in DeFi](https://chainaware.ai/blog/the-web3-agentic-economy-how-ai-agents-are-replacing-human-teams-in-defi/) — How autonomous AI agents are taking over DeFi operations and decision-making

---

## 🧾 License

MIT (for client examples).
Server implementation and backend logic are proprietary and remain private.