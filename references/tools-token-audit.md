# Token Audit — Reference

**Tool IDs:** `run_token_audit` · `get_token_audit_result`
**MCP Endpoint:** `https://prediction.mcp.chainaware.ai/sse`

A two-step deep audit pipeline for token contracts. Covers ownership control, liquidity health,
supply/mint risk, transfer integrity, approve/permit safety, reentrancy, and honeypot behavior —
producing a single 0–100 aggregate risk score with a verdict.

Workflow:
```
run_token_audit → if status == "queued" → poll get_token_audit_result until audit_status == "complete"
```

`run_token_audit` is a **get-or-create** call. If a completed audit already exists for the contract
it returns the full report immediately (no second call needed). Only if the audit is new does it
queue a job and require polling.

---

## Supported Networks

`eth` · `bsc` · `base` · `arbitrum` · `avalanche` · `optimism` · `polygon`

> Note: network values are **lowercase** for these tools (unlike `predictive_fraud` / `predictive_rug_pull`).

---

## Tool 1 — `run_token_audit`

### Input Schema

| Field              | Type   | Required | Description                                                                          |
|-------------------|--------|----------|--------------------------------------------------------------------------------------|
| `contract_address` | string | ✅        | Token contract address                                                               |
| `network`          | string | ✅        | One of: `eth`, `bsc`, `base`, `arbitrum`, `avalanche`, `optimism`, `polygon`         |

### Output — Two Possible Shapes

**Shape A — Audit already exists (`audit_status: "complete"`):**
Returns the full risk report immediately. Same schema as `get_token_audit_result`. No further
tool calls needed — answer the user directly from this response.

**Shape B — New audit queued:**
```json
{
  "contract_address": "string",
  "chain": "string",
  "job_id": "string",
  "status": "queued",
  "message": "string"
}
```
Store `job_id` and begin polling `get_token_audit_result` until `audit_status == "complete"`.

---

## Tool 2 — `get_token_audit_result`

### Input Schema

| Field              | Type   | Required | Description                                                                          |
|-------------------|--------|----------|--------------------------------------------------------------------------------------|
| `contract_address` | string | ✅        | Same contract address passed to `run_token_audit`                                    |
| `network`          | string | ✅        | Same network passed to `run_token_audit`                                             |

### Output Schema

```json
{
  "contract_address": "string",
  "chain": "string",
  "audit_status": "queued | running | complete",
  "token_name": "string",
  "token_symbol": "string",
  "token_decimals": 18,
  "token_creator": "string",
  "token_feeder": "string",
  "source_verified": true,
  "is_proxy": false,
  "behavioral_is_honeypot": false,
  "honeypot_analysis": {
    "verdict": "string",
    "score": 0,
    "findings": [
      {
        "rule": "string",
        "severity": "info | low | medium | high | critical",
        "function": "string | null",
        "detail": "string"
      }
    ],
    "flags": ["string"]
  },
  "aggregate": {
    "verdict": "string",
    "risk_score": 0,
    "primary_signal": "string",
    "simulate": true,
    "version": "string",
    "duration_ms": 0,
    "last_run": "ISO-8601"
  },
  "quick_stats": [
    { "label": "string", "value": "string", "tone": "good | warn | bad" }
  ],
  "modules": {
    "ownership": {
      "status": "pass | warn | fail",
      "risk_score": 0,
      "owner_address": "string",
      "owner_is_eoa": true,
      "owner_is_renounced": false,
      "blast_radius": "none | low | medium | high | critical",
      "can_mint": false,
      "can_pause": false,
      "can_blacklist": false,
      "can_upgrade": false,
      "can_drain": false,
      "has_timelock": false,
      "has_shadow": false
    },
    "liquidity": {
      "status": "pass | warn | fail",
      "summary": "string",
      "risk_score": 0,
      "pool_count": 0,
      "unknown_pool": false,
      "invariants": [
        {
          "code": "string",
          "label": "string",
          "status": "pass | fail",
          "severity": "string",
          "detail": "string"
        }
      ]
    },
    "supply": {
      "status": "pass | warn | fail",
      "risk_score": 0,
      "has_mint": false,
      "deployer_pct": 0.0,
      "hidden_mint": false
    },
    "transfer": {
      "status": "pass | warn | fail",
      "risk_score": 0,
      "method": "string",
      "monitoring": false,
      "inv_t1_pass": true,
      "inv_t2_pass": true,
      "inv_t3_pass": true,
      "inv_t4_pass": true,
      "inv_t5_pass": true,
      "inv_t6_pass": true,
      "inv_t7_pass": true
    },
    "pausability": {
      "status": "pass | warn | fail",
      "applicable": false
    },
    "approve": {
      "status": "pass | warn | fail",
      "risk_score": 0,
      "method": "string",
      "coverage": "string",
      "inv1_pass": true,
      "inv2_pass": true,
      "inv3_pass": true,
      "inv5_pass": true,
      "inv6_pass": true
    },
    "permit": {
      "status": "pass | warn | fail",
      "applicable": false
    },
    "reentrancy": {
      "status": "pass | warn | fail",
      "risk_score": 0
    }
  }
}
```

---

## Key Output Fields Explained

### `aggregate`

| Field           | Description                                                                        |
|----------------|------------------------------------------------------------------------------------|
| `risk_score`    | 0–100 composite risk score. Higher = more dangerous.                               |
| `verdict`       | Human-readable verdict (e.g. "High Risk", "Low Risk", "Safe")                     |
| `primary_signal`| The single most dangerous finding driving the score                                |

### `aggregate.risk_score` Interpretation

| Range   | Risk Level | Recommended Action                                  |
|---------|------------|-----------------------------------------------------|
| 0–20    | Low        | Contract appears safe to interact with              |
| 21–50   | Medium     | Proceed with caution; monitor ownership and liquidity |
| 51–80   | High       | Warn users prominently before they deposit          |
| 81–100  | Critical   | Block interaction or listing entirely               |

### `modules.ownership`

| Field               | Description                                                       |
|--------------------|-------------------------------------------------------------------|
| `owner_is_renounced`| `true` means no one can call privileged functions — safer        |
| `blast_radius`      | Worst-case impact if the owner acts maliciously                   |
| `can_mint`          | Owner can create new tokens (supply inflation risk)               |
| `can_blacklist`     | Owner can freeze specific addresses                               |
| `can_drain`         | Owner has a direct withdrawal function — extreme danger           |
| `has_shadow`        | Hidden/proxy owner detected                                       |

### `modules.liquidity.invariants`

Each invariant is a specific check (e.g. "LP tokens are locked", "liquidity is sufficient").
A `fail` status on any invariant is a red flag worth surfacing to the user.

### `honeypot_analysis`

| Field     | Description                                                            |
|----------|------------------------------------------------------------------------|
| `verdict` | "No Honeypot", "Likely Honeypot", "Confirmed Honeypot"                 |
| `score`   | 0–100 honeypot confidence score                                        |
| `flags`   | List of specific honeypot indicators detected                          |
| `findings`| Per-rule findings with severity and the function involved              |

---

## Polling Logic

When `run_token_audit` returns `status: "queued"`, poll `get_token_audit_result` with the
same `contract_address` + `network` until `audit_status == "complete"`.

While `audit_status` is `"queued"` or `"running"`, module fields may be null or stale —
do not present them as final results.

```javascript
// Recommended polling pattern
async function awaitAudit(contractAddress, network) {
  while (true) {
    const result = await client.call("get_token_audit_result", {
      contract_address: contractAddress,
      network
    });
    if (result.audit_status === "complete") return result;
    await new Promise(r => setTimeout(r, 5000)); // poll every 5s
  }
}
```

---

## Example Agent Prompts

```
"Audit this token contract for me: 0x..."
"Is this BSC token safe? 0x..."
"Run a risk scan on this contract before I buy"
"Check if this address is a honeypot"
"What is the ownership risk for this token on Arbitrum?"
"Does this contract have hidden mint functions?"
"Is liquidity locked for this token?"
"Give me a full breakdown of this contract's risks"
```

---

## Example API Call (Node.js)

```javascript
// Step 1 — request audit (get-or-create)
const audit = await client.call("run_token_audit", {
  contract_address: "0xContractAddressHere",
  network: "eth"
});

if (audit.audit_status === "complete") {
  // Cached result — answer immediately
  console.log("Risk score:", audit.aggregate.risk_score);
  console.log("Verdict:", audit.aggregate.verdict);
} else {
  // Queued — poll for completion
  console.log("Audit queued, job_id:", audit.job_id);

  let result;
  while (true) {
    result = await client.call("get_token_audit_result", {
      contract_address: "0xContractAddressHere",
      network: "eth"
    });
    if (result.audit_status === "complete") break;
    await new Promise(r => setTimeout(r, 5000));
  }

  console.log("Risk score:", result.aggregate.risk_score);
  console.log("Verdict:", result.aggregate.verdict);
  console.log("Can mint:", result.modules.ownership.can_mint);
  console.log("Honeypot:", result.honeypot_analysis.verdict);
}
```

---

## Example API Call (Python)

```python
import time

# Step 1 — request audit
audit = client.call("run_token_audit", {
    "contract_address": "0xContractAddressHere",
    "network": "bsc"
})

if audit.get("audit_status") == "complete":
    print("Risk score:", audit["aggregate"]["risk_score"])
    print("Verdict:", audit["aggregate"]["verdict"])
else:
    print("Queued, polling...")
    while True:
        result = client.call("get_token_audit_result", {
            "contract_address": "0xContractAddressHere",
            "network": "bsc"
        })
        if result["audit_status"] == "complete":
            break
        time.sleep(5)

    print("Risk score:", result["aggregate"]["risk_score"])
    print("Can mint:", result["modules"]["ownership"]["can_mint"])
    print("Honeypot:", result["honeypot_analysis"]["verdict"])
```

---

## How the Audit Works

Eight specialist modules each produce a `risk_score` and `status`. Their scores feed a
weighted aggregate:

| Module       | What it checks                                                          |
|-------------|-------------------------------------------------------------------------|
| `ownership`  | Who controls the contract and what powers they hold                     |
| `liquidity`  | Pool depth, lock status, and withdrawal risk                            |
| `supply`     | Mint functions, deployer token concentration, hidden inflation          |
| `transfer`   | Transfer function invariants — can transfers be blocked or hijacked?   |
| `pausability`| Whether transfers can be paused by a privileged address                 |
| `approve`    | ERC-20 approve/allowance correctness and front-running exposure         |
| `permit`     | EIP-2612 permit signature safety                                        |
| `reentrancy` | Re-entrant call paths that could be exploited for fund drainage         |

---

## Use Cases

- **Launchpads** — block high-risk contracts before listing
- **DEXes** — auto-scan new pools; surface risk score in the UI
- **Wallets** — warn users before they approve or buy a risky token
- **Investors** — due diligence before entering a position
- **Insurance protocols** — price cover based on per-module risk scores
- **DeFi aggregators** — exclude high-risk tokens from yield routing

---

## Composability

- Run `predictive_rug_pull` alongside this for a second opinion on rug pull probability
- Use `predictive_fraud` on `token_creator` / `token_feeder` to assess the deployer's behavioral history
- Combine with `token_rank_single` to assess both contract safety and holder community quality

---

## Error Codes

| Code  | Meaning                                                  |
|-------|----------------------------------------------------------|
| `400` | Malformed `contract_address` or `network`                |
| `500` | Temporary backend failure — retry after a short delay    |

---

## Further Reading

- Complete Product Guide: https://chainaware.ai/blog/chainaware-ai-products-complete-guide/
- Rug Pull Detector Guide: https://chainaware.ai/blog/chainaware-rugpull-detector-guide/
