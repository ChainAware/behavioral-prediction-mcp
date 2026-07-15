# Agent Trust Score — Reference

**Tool IDs:** `agents_trust_score_list` · `agents_trust_score_single`
**MCP Endpoint:** `https://prediction.mcp.chainaware.ai/sse`

The Agent Trust Score (0–1000) measures how safe it is to interact with any ERC-8004 registered
AI agent. Unlike voting-based reputation systems — where agents can upvote each other to manufacture
trust — this score is derived entirely from **on-chain behavioral history**. It cannot be earned in
hours or faked with fresh wallets. It reflects the real-world track record of the human or entity
controlling the agent.

As agentic commerce scales — with AI agents autonomously completing purchases on behalf of consumers
across ChatGPT, Google Gemini, and Shopify — the question of which agents can be trusted to transact
is no longer theoretical. ChainAware answers it with on-chain evidence, not peer endorsements.

Typical workflow:
```
agents_trust_score_list → discover agents, get agent_id + chain_id → agents_trust_score_single → deep profile
```

---

## Tool 1 — `agents_trust_score_list`

Returns a paginated list of all ERC-8004 registered AI agents with their trust scores.
Use this for discovery, ranking, and filtering agents before interacting with them.

### Input Schema

| Field              | Type   | Required | Description                                                              |
|-------------------|--------|----------|--------------------------------------------------------------------------|
| `page`             | string | ✅        | Page number for pagination (1-indexed)                                   |
| `limit`            | string | ✅        | Number of results per page                                               |
| `sort_by`          | string |          | Field to sort by (e.g. `registered_at`, `trust_score`, `reputation_score`) |
| `sort_order`       | string |          | `asc` or `desc` (default: `desc`). Required if `sort_by` is provided    |
| `registered_after` | string |          | ISO-8601 datetime — filter to agents registered after this date          |

### Output Schema

```json
{
  "total": 0,
  "page": 1,
  "limit": 10,
  "results": [
    {
      "chain_id": 1,
      "agent_id": 0,
      "owner_address": "string",
      "agent_wallet": "string",
      "agent_uri": "string",
      "meta_name": "string",
      "registered_at": "ISO-8601",
      "reputation_score": 0,
      "trust_score": 0,
      "trust_tier": "string"
    }
  ]
}
```

### Key List Fields

| Field             | Description                                                                   |
|------------------|-------------------------------------------------------------------------------|
| `agent_id`        | Unique agent identifier — required for `agents_trust_score_single`            |
| `chain_id`        | Chain where the agent is registered — required for `agents_trust_score_single`|
| `agent_wallet`    | The agent's operational wallet address                                        |
| `owner_address`   | The human/entity who registered and controls the agent                        |
| `trust_score`     | 0–1000 composite trust score derived from on-chain behavioral history         |
| `reputation_score`| Raw reputation score before trust normalization                               |
| `trust_tier`      | Human-readable tier label (see Tier Interpretation below)                     |
| `agent_uri`       | URI to the agent's ERC-8004 metadata JSON                                     |

---

## Tool 2 — `agents_trust_score_single`

Returns a full in-depth trust profile for a single ERC-8004 registered agent.
Requires `agent_id` + `chain_id` from `agents_trust_score_list`.

### Input Schema

| Field      | Type    | Required | Description                                                             |
|-----------|---------|----------|-------------------------------------------------------------------------|
| `agent_id` | integer | ✅        | Agent ID from `agents_trust_score_list`                                 |
| `chain_id` | integer | ✅        | Chain ID where the agent is registered (e.g. `1` = ETH, `56` = BNB)    |

### Output Schema

```json
{
  "agent_id": 0,
  "chain": "string",
  "chain_id": 1,
  "owner_address": "string",
  "agent_wallet": "string",
  "wallet_verified": true,
  "agent_uri": "string",
  "registered_at": "ISO-8601",
  "fetched_at": "ISO-8601",
  "error": "string | null",
  "meta_name": "string",
  "meta_description": "string",
  "meta_image": "string",
  "metadata_json": {
    "type": "string",
    "name": "string",
    "description": "string",
    "image": "string",
    "active": true,
    "supportedTrust": ["string"]
  },
  "registration": {
    "agent_name": "string",
    "agent_desc": "string",
    "fetch_status": "string",
    "fetched_at": "ISO-8601",
    "raw_json": {
      "type": "string",
      "name": "string",
      "description": "string",
      "image": "string",
      "active": true,
      "supportedTrust": ["string"]
    }
  },
  "wallets": [
    {
      "wallet_chain_id": 1,
      "wallet_address": "string",
      "source": "registry",
      "fetched_at": "ISO-8601"
    }
  ],
  "reputation_score": "string",
  "trust_score": 0,
  "trust_tier": "string",
  "trust_flags": ["string"]
}
```

### Key Single Fields

| Field             | Description                                                                         |
|------------------|-------------------------------------------------------------------------------------|
| `trust_score`     | 0–1000 composite score. Higher = more trustworthy.                                  |
| `trust_tier`      | Human-readable tier (see Tier Interpretation below)                                 |
| `trust_flags`     | Array of specific risk or trust signals flagged for this agent                      |
| `wallet_verified` | Whether the agent's declared wallet matches on-chain registration                   |
| `wallets`         | All wallets associated with this agent across chains                                |
| `supportedTrust`  | Trust frameworks the agent declares support for (from ERC-8004 metadata)            |
| `error`           | Non-null if metadata fetch failed — agent may have an invalid or unreachable URI    |

---

## Trust Score Interpretation

| Score Range | Trust Tier   | Meaning                                                            |
|------------|--------------|---------------------------------------------------------------------|
| 800–1000   | Elite        | Exceptionally strong on-chain history — high confidence to transact |
| 600–799    | High         | Strong track record — suitable for most agentic commerce use cases  |
| 400–599    | Moderate     | Some history present — proceed with transaction limits              |
| 200–399    | Low          | Thin or mixed history — enhanced scrutiny recommended               |
| 1–199      | Very Low     | Insufficient or negative history — avoid high-value interactions    |
| 0          | Fraud / New  | Flagged as fraudulent or no verifiable on-chain history             |

---

## Common Chain IDs

| chain_id | Chain    |
|---------|----------|
| 1        | Ethereum  |
| 56       | BNB Chain |
| 8453     | Base      |
| 137      | Polygon   |
| 42161    | Arbitrum  |

---

## Example Agent Prompts

```
"Give me a list of AI agents and their trust scores"
"Which registered agents have the highest trust score?"
"What is the trust score for agent ID 123 on chain 56?"
"Show me all agents registered after 2025-01-01"
"Is this ERC-8004 agent safe to transact with?"
"List the top 10 most trusted AI agents on BNB Chain"
"Deep profile for agent #42 on Ethereum"
"What trust flags does this agent have?"
"Which agents support the ERC-8004 trust framework?"
```

---

## Example API Call (Node.js)

```javascript
// Step 1 — list agents, sorted by trust score
const list = await client.call("agents_trust_score_list", {
  page: "1",
  limit: "10",
  sort_by: "trust_score",
  sort_order: "desc"
});

console.log(`Total agents: ${list.total}`);
list.results.forEach(agent => {
  console.log(`${agent.meta_name} — Trust: ${agent.trust_score} (${agent.trust_tier})`);
});

// Step 2 — deep profile for a specific agent
const profile = await client.call("agents_trust_score_single", {
  agent_id: list.results[0].agent_id,
  chain_id: list.results[0].chain_id
});

console.log("Agent:", profile.meta_name);
console.log("Trust Score:", profile.trust_score);
console.log("Trust Tier:", profile.trust_tier);
console.log("Flags:", profile.trust_flags);
console.log("Wallet verified:", profile.wallet_verified);
```

---

## Example API Call (Python)

```python
# Step 1 — list and filter agents
list_result = client.call("agents_trust_score_list", {
    "page": "1",
    "limit": "20",
    "sort_by": "trust_score",
    "sort_order": "desc",
    "registered_after": "2025-01-01T00:00:00Z"
})

print(f"Total: {list_result['total']}")
for agent in list_result["results"]:
    print(f"{agent['meta_name']} — {agent['trust_score']} ({agent['trust_tier']})")

# Step 2 — deep profile
profile = client.call("agents_trust_score_single", {
    "agent_id": list_result["results"][0]["agent_id"],
    "chain_id": list_result["results"][0]["chain_id"]
})

print(f"Score: {profile['trust_score']}")
print(f"Tier: {profile['trust_tier']}")
print(f"Flags: {profile['trust_flags']}")
```

---

## Use Cases

- **Agentic commerce platforms** — verify an agent's trustworthiness before allowing it to execute purchases or payments on behalf of users
- **AI agent marketplaces** — rank and surface agents by trust score; surface `trust_flags` prominently for low-score agents
- **DeFi protocols** — gate access to high-value operations (flash loans, large swaps) based on agent trust tier
- **Multi-agent pipelines** — screen counterparty agents before delegating tasks or sharing funds
- **Developer due diligence** — verify an agent's ERC-8004 registration and on-chain history before integrating it into a product
- **User-facing agent selectors** — display trust scores so end users can choose which agent to authorize for their wallet

---

## Composability

- Use `predictive_fraud` on `agent_wallet` or `owner_address` for a deeper behavioral fraud check
- Use `predictive_behaviour` on `agent_wallet` to understand the agent's full on-chain behavioral profile
- The existing `chainaware-agent-screener` subagent screens agents by wallet address using fraud + behaviour signals — complementary to the ERC-8004 registry approach here

---

## ERC-8004 Context

ERC-8004 is an on-chain agent registration standard. Agents registered under ERC-8004 publish
a metadata URI (`agent_uri`) containing their name, description, declared trust frameworks
(`supportedTrust`), and associated wallets. ChainAware scores these agents by analyzing the
on-chain behavioral history of their registered wallets — not by accepting their self-declared
metadata at face value.

---

## Error Codes

| Code  | Meaning                                                  |
|-------|----------------------------------------------------------|
| `400` | Malformed `agent_id`, `chain_id`, `page`, or `limit`     |
| `500` | Temporary backend failure — retry after a short delay    |

---

## Further Reading

- Complete Product Guide: https://chainaware.ai/blog/chainaware-ai-products-complete-guide/
- Prediction MCP Developer Guide: https://chainaware.ai/blog/prediction-mcp-for-ai-agents-personalize-decisions-from-wallet-behavior/
