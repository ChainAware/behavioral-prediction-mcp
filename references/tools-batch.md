# Batch Processing Tools — Reference

**Tool IDs:** `predictive_fraud_batch` · `predictive_behaviour_batch` · `check_job_status` · `get_job_results`
**MCP Endpoint:** `https://prediction.mcp.chainaware.ai/sse`

Async batch pipeline for running fraud detection or behavioural analysis across large lists of wallet addresses. The four tools form a sequential pipeline: schedule a job → check its status → retrieve results.

---

## Pipeline Overview

```
predictive_fraud_batch
predictive_behaviour_batch  ──→  check_job_status  ──→  get_job_results
        (schedule)                  (poll progress)       (fetch data)
```

1. **Schedule** — submit a list of addresses; receive `job_id` + `signature` immediately
2. **Check** — poll until status is `completed` or `partial`
3. **Retrieve** — fetch the full per-wallet results

> Always store both `job_id` and `signature` from the schedule step — both are required for every follow-up call.

---

## Tool 1: `predictive_fraud_batch`

Schedule a batch fraud detection job for a list of wallet addresses (~98% accuracy). Returns immediately with a job handle; does not wait for results.

### Supported Networks

`ETH` · `BNB` · `POLYGON` · `TON` · `BASE` · `TRON` · `HAQQ`

### Input Schema

| Field       | Type            | Required | Description                                                                 |
|-------------|-----------------|----------|-----------------------------------------------------------------------------|
| `apiKey`    | string          | ✅        | ChainAware API key (obtain at chainaware.ai/pricing)                        |
| `network`   | string          | ✅        | One of: `ETH`, `BNB`, `POLYGON`, `TON`, `BASE`, `TRON`, `HAQQ`             |
| `addresses` | array[objects]  | ✅        | List of wallet address objects to evaluate                                  |

### Output Schema

```json
{
  "message": "Job scheduled successfully.",
  "job_id": "0fc5897a-ad64-4f21-88b5-1274d1cfec46",
  "signature": "260866090d88bf61bdfb54f0533fe876bfd8ded7339691c50ada9de59a48124a",
  "total_items": 5,
  "chunks_enqueued": 1,
  "status": "pending"
}
```

### Example Agent Prompts

```
"Run fraud batch for this list of addresses on ETH."
"What is the fraudulent status of these addresses on BNB?"
"Screen all wallets from this CSV on BASE network."
```

### Example API Call (Node.js)

```javascript
const result = await client.call("predictive_fraud_batch", {
  apiKey: process.env.CHAINAWARE_API_KEY,
  network: "ETH",
  addresses: [
    { walletAddress: "0xABC..." },
    { walletAddress: "0xDEF..." }
  ]
});

const { job_id, signature } = result; // store both for follow-up calls
```

---

## Tool 2: `predictive_behaviour_batch`

Schedule a batch behavioural analysis job — projects intent, experience, risk profile, and recommendations for a list of wallet addresses. Returns immediately with a job handle.

### Supported Networks

`ETH` · `BNB` · `BASE` · `HAQQ` · `SOLANA`

### Input Schema

| Field       | Type            | Required | Description                                                                 |
|-------------|-----------------|----------|-----------------------------------------------------------------------------|
| `apiKey`    | string          | ✅        | ChainAware API key (obtain at chainaware.ai/pricing)                        |
| `network`   | string          | ✅        | One of: `ETH`, `BNB`, `BASE`, `HAQQ`, `SOLANA`                             |
| `addresses` | array[objects]  | ✅        | List of wallet address objects to evaluate                                  |

### Output Schema

Same as `predictive_fraud_batch`:

```json
{
  "message": "Job scheduled successfully.",
  "job_id": "0fc5897a-ad64-4f21-88b5-1274d1cfec46",
  "signature": "260866090d88bf61bdfb54f0533fe876bfd8ded7339691c50ada9de59a48124a",
  "total_items": 5,
  "chunks_enqueued": 1,
  "status": "pending"
}
```

### Example Agent Prompts

```
"What will these addresses do next on ETH?"
"Are these users high-risk or experienced? [list of addresses]"
"Recommend DeFi strategies for all wallets in this list on BASE."
```

### Example API Call (Node.js)

```javascript
const result = await client.call("predictive_behaviour_batch", {
  apiKey: process.env.CHAINAWARE_API_KEY,
  network: "ETH",
  addresses: [
    { walletAddress: "0xABC..." },
    { walletAddress: "0xDEF..." }
  ]
});

const { job_id, signature } = result; // store both for follow-up calls
```

---

## Tool 3: `check_job_status`

Check the progress of a scheduled batch job. Returns counts only (completed / failed / pending) — no wallet data. Call this to determine whether results are ready.

**Do not call `get_job_results` until status is `completed` or `partial`.**

### Input Schema

| Field       | Type   | Required | Description                                                              |
|-------------|--------|----------|--------------------------------------------------------------------------|
| `job_id`    | string | ✅        | The `job_id` returned by `predictive_fraud_batch` or `predictive_behaviour_batch` |
| `signature` | string | ✅        | The `signature` returned by the same schedule call. Required for access  |

### Output Schema

```json
{
  "job_id": "0fc5897a-ad64-4f21-88b5-1274d1cfec46",
  "status": "partial",
  "chain": "ETH",
  "total_items": 5,
  "completed_items": 1,
  "failed_items": 4,
  "pending_items": 0,
  "expires_at": "2026-07-01T13:51:20.000Z"
}
```

### Status Values

| Value        | Meaning                                                      | Next Action                  |
|-------------|--------------------------------------------------------------|------------------------------|
| `pending`    | Job queued, not yet started                                  | Wait and check again         |
| `processing` | Job is actively running                                      | Wait and check again         |
| `partial`    | Some wallets done, some failed                               | Safe to call `get_job_results` |
| `completed`  | All wallets processed                                        | Call `get_job_results`       |

### Example API Call (Node.js)

```javascript
const status = await client.call("check_job_status", {
  job_id: "0fc5897a-ad64-4f21-88b5-1274d1cfec46",
  signature: "260866090d88bf61bdfb54f0533fe876bfd8ded7339691c50ada9de59a48124a"
});

if (status.status === "completed" || status.status === "partial") {
  // safe to fetch results
}
```

---

## Tool 4: `get_job_results`

Retrieve the full per-wallet results from a completed or partially completed batch job. Returns the same rich schema as the single-wallet tools (`predictive_behaviour` / `predictive_fraud`).

**Only call this when `check_job_status` returns `completed` or `partial`.**

### Input Schema

| Field       | Type   | Required | Description                                                              |
|-------------|--------|----------|--------------------------------------------------------------------------|
| `job_id`    | string | ✅        | The `job_id` returned by the schedule call                               |
| `signature` | string | ✅        | The `signature` returned by the same schedule call. Required for access  |

### Output Schema

```json
{
  "message": "string",
  "data": [
    {
      "walletAddress": "string",
      "status": "Fraud | Not Fraud | New Address",
      "probabilityFraud": "0.00–1.00",
      "token": "string | null",
      "chain": "string",
      "lastChecked": "ISO-8601 timestamp",
      "forensic_details": { "...": "see tools-fraud.md for full schema" },
      "categories": [{ "Category": "string", "Count": 0 }],
      "riskProfile": [{ "Category": "Risk_Profile", "Balance_age": 0.0 }],
      "segmentInfo": "string (JSON encoded)",
      "experience": { "Type": "string", "Value": 0 },
      "intention": { "Type": "string", "Value": { "Prob_Lend": "Low | Medium | High", "...": "..." } },
      "protocols": [{ "Protocol": "string", "Count": 0 }],
      "userDetails": {
        "wallet_age_days": 0,
        "total_balance_usd": 0.0,
        "transaction_count": 0,
        "wallet_rank": 0
      },
      "riskCapability": 0,
      "recommendation": { "Type": "string", "Value": ["string"] },
      "checked_times": 0,
      "createdAt": "ISO-8601 timestamp",
      "updatedAt": "ISO-8601 timestamp",
      "sanctionData": [{ "isSanctioned": false, "...": "..." }]
    }
  ]
}
```

> The per-wallet schema mirrors `predictive_behaviour` output. See `tools-behaviour.md` for full field descriptions.

### Example API Call (Node.js)

```javascript
const results = await client.call("get_job_results", {
  job_id: "0fc5897a-ad64-4f21-88b5-1274d1cfec46",
  signature: "260866090d88bf61bdfb54f0533fe876bfd8ded7339691c50ada9de59a48124a"
});

for (const wallet of results.data) {
  console.log(wallet.walletAddress, wallet.status, wallet.probabilityFraud);
}
```

---

## Full Pipeline Example (Node.js)

```javascript
// 1. Schedule
const job = await client.call("predictive_behaviour_batch", {
  apiKey: process.env.CHAINAWARE_API_KEY,
  network: "ETH",
  addresses: walletList.map(w => ({ walletAddress: w }))
});
const { job_id, signature } = job;

// 2. Poll until ready
let ready = false;
while (!ready) {
  await new Promise(r => setTimeout(r, 5000)); // wait 5s between polls
  const statusRes = await client.call("check_job_status", { job_id, signature });
  ready = statusRes.status === "completed" || statusRes.status === "partial";
}

// 3. Fetch results
const results = await client.call("get_job_results", { job_id, signature });
console.log(results.data);
```

---

## Use Cases

- **Airdrop screening** — batch screen thousands of airdrop applicants for fraud before token distribution
- **Cohort analysis** — run behavioural prediction across an entire user base in one job
- **Sybil detection** — process large DAO voter lists without hitting single-wallet rate limits
- **Exchange onboarding** — pre-screen a bulk import of user wallets overnight
- **Portfolio monitoring** — re-score all wallets in a lending book on a scheduled basis

---

## Error Codes

| Code  | Meaning                                                  |
|-------|----------------------------------------------------------|
| `403` | Invalid or missing `apiKey` or `signature`               |
| `400` | Malformed `network` or `walletAddress`                   |
| `500` | Temporary backend failure — retry after a short delay    |

---

## Further Reading

- Fraud tool schema: `references/tools-fraud.md`
- Behaviour tool schema: `references/tools-behaviour.md`
- Complete Product Guide: https://chainaware.ai/blog/chainaware-ai-products-complete-guide/
