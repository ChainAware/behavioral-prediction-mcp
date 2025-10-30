# 🧠 Behavioural Prediction MCP Server

**MCP Server Name:** Behavioural Prediction MCP

**Category:** Web3 / Security / DeFi Analytics

**Status:** Public tools – Private backend

**Access:** By request (API key)

**Server URL:** [https://prediction.mcp.chainaware.ai/]

**Repository:** [https://github.com/ChainAware/behavioral-prediction-mcp]


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

| Name            | Type   | Required | Description                                                               |
| --------------- | ------ | -------- | ------------------------------------------------------------------------- |
| `apiKey`        | string | ✅        | API key for authentication                                               |
| `network`       | string | ✅        | Blockchain network (`ETH`, `BNB`,`BASE`,`HAQQ`)                          |
| `walletAddress` | string | ✅        | The wallet address to evaluate                                           |



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

## 🔌 Integration Notes

* Compatible with all MCP clients (Node, Python, Browser)
* Uses Server-Sent Events (SSE) for real-time responses
* JSON schemas match MCP spec
* Rate limits may apply
* API key required for production endpoints

---

## 🔒 Access Policy

The MCP server requires an API key for production usage.
To request access:

* You can subscribe to listed available plans via: https://chainaware.ai/pricing

---

## 🧾 License

MIT (for client examples).
Server implementation and backend logic are proprietary and remain private.