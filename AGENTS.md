# ChainAware Subagent Index

Machine-readable index of all 20 Claude Code subagents in `.claude/agents/`.
Each agent is a specialist that handles a specific Web3 intelligence task using the
ChainAware Behavioral Prediction MCP (`https://prediction.mcp.chainaware.ai/sse`).

**Quick setup:**
```bash
claude mcp add --transport sse chainaware-behavioral-prediction \
  https://prediction.mcp.chainaware.ai/sse --header "X-API-Key: YOUR_KEY"
export CHAINAWARE_API_KEY="your-key-here"
```

---

## Agent Directory

### chainaware-analyst
**File:** `.claude/agents/chainaware-analyst.md`
**Model:** claude-sonnet-4-6
**Tools:** `predictive_fraud`, `predictive_behaviour`, `predictive_rug_pull`
**Purpose:** Full due diligence. Combines fraud, behaviour, and rug pull analysis into a comprehensive wallet or contract report.
**Triggers:** "full audit of 0x...", "due diligence on this wallet", "complete analysis", "deep dive on this address"
**Input:** wallet or contract address + network
**Output:** Fraud probability, behaviour profile, rug pull risk, reputation score, recommendations

---

### chainaware-fraud-detector
**File:** `.claude/agents/chainaware-fraud-detector.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`
**Purpose:** Fast wallet fraud screening. Returns fraud status, probability, and forensic AML flags.
**Triggers:** "is this wallet safe?", "fraud check on 0x...", "AML screen", "is this address suspicious?", "check before I transact"
**Input:** wallet address + network
**Output:** Status (Fraud / Not Fraud / New Address), probabilityFraud (0–1), forensic flags

---

### chainaware-rug-pull-detector
**File:** `.claude/agents/chainaware-rug-pull-detector.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_rug_pull`, `predictive_fraud`
**Purpose:** Smart contract and LP safety checks before depositing funds.
**Triggers:** "will this pool rug pull?", "is this contract safe?", "check this LP", "rug pull risk", "should I ape in?"
**Input:** smart contract or LP address + network (ETH, BNB, BASE, HAQQ)
**Output:** Rug pull probability, risk tier, deployer risk, forensic breakdown, recommendation

---

### chainaware-wallet-marketer
**File:** `.claude/agents/chainaware-wallet-marketer.md`
**Model:** claude-sonnet-4-6
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Generates a hyper-personalised marketing message (≤20 words) calibrated to the wallet's behaviour profile.
**Triggers:** "write a message for this wallet", "personalise outreach for 0x...", "convert this user", "marketing for this address"
**Input:** wallet address + network
**Output:** One personalised message (≤20 words) + rationale

---

### chainaware-reputation-scorer
**File:** `.claude/agents/chainaware-reputation-scorer.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Calculates a numeric reputation score using the ChainAware formula.
**Formula:** `1000 × (experience + 1) × (willingness_to_take_risk + 1) × (1 - fraud_probability)`
**Triggers:** "reputation score for 0x...", "score this wallet", "rank these wallets", "which wallet is better?"
**Input:** wallet address + network
**Output:** Reputation score (0–4000), tier label, component breakdown

---

### chainaware-aml-scorer
**File:** `.claude/agents/chainaware-aml-scorer.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`
**Purpose:** AML compliance scoring for KYC, regulatory, and exchange onboarding workflows.
**Formula:** `(1 - probabilityFraud) × 100` — returns 0 if any forensic flag is present
**Triggers:** "AML score for 0x...", "is this wallet AML compliant?", "KYC screening", "compliance check", "run AML check"
**Input:** wallet address + network
**Output:** AML score (0–100), pass/fail verdict, forensic flag breakdown

---

### chainaware-trust-scorer
**File:** `.claude/agents/chainaware-trust-scorer.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`
**Purpose:** Returns a simple trust score as `1 - fraud_probability`.
**Triggers:** "trust score for 0x...", "how much do I trust this wallet?", "trustworthiness rating", "confidence score"
**Input:** wallet address + network
**Output:** Trust score (0.00–1.00)

---

### chainaware-wallet-ranker
**File:** `.claude/agents/chainaware-wallet-ranker.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`
**Purpose:** Returns a wallet's global experience rank and leaderboard position.
**Triggers:** "what is the rank of 0x...", "rank this wallet", "how good is this wallet?", "global rank", "wallet leaderboard"
**Input:** wallet address + network
**Output:** Experience score (0–100), global rank, experience tier, wallet age, transaction count

---

### chainaware-token-ranker
**File:** `.claude/agents/chainaware-token-ranker.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `token_rank_list`
**Purpose:** Discovers and ranks tokens by the strength of their holder community.
**Triggers:** "top AI tokens", "best DeFi tokens on ETH", "rank tokens by community", "strongest holder base", "token leaderboard"
**Input:** network + optional category (AI Token, RWA Token, DeFi Token, DeFAI Token, DePIN Token) + sort preference
**Output:** Ranked token list with community rank, normalised rank, holder count, chain, category

---

### chainaware-token-analyzer
**File:** `.claude/agents/chainaware-token-analyzer.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `token_rank_single`, `predictive_fraud`
**Purpose:** Deep-dive into a single token: community rank, top holders, global wallet rank, and holder fraud screening.
**Triggers:** "who holds this token?", "token rank for 0x...", "best holders of this contract", "holder analysis", "is this token held by whales?"
**Input:** contract address + network
**Output:** Token community rank, top holders with global rank, fraud screening on top holders

---

### chainaware-onboarding-router
**File:** `.claude/agents/chainaware-onboarding-router.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Routes wallets to the correct onboarding flow based on on-chain experience level.
**Triggers:** "which onboarding flow for 0x...", "is this user a beginner?", "skip onboarding for this wallet?", "route this wallet", "first-time user check"
**Input:** wallet address + network
**Output:** Onboarding route (Beginner / Intermediate / Power User / Skip), rationale, recommended first steps

---

### chainaware-whale-detector
**File:** `.claude/agents/chainaware-whale-detector.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Classifies wallets into whale tiers for VIP treatment, fee discounts, and governance weighting.
**Triggers:** "is this a whale?", "VIP wallet", "whale tier", "find whales", "classify this wallet", "high-value wallet check"
**Input:** wallet address + network (supports batch)
**Output:** Tier (Mega Whale / Whale / Emerging Whale / Not a Whale), domain (DeFi / NFT / Trading / Yield), status (Active / Dormant), recommended treatment

---

### chainaware-defi-advisor
**File:** `.claude/agents/chainaware-defi-advisor.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Returns personalised DeFi product recommendations calibrated to the wallet's experience and risk appetite.
**Triggers:** "what DeFi products suit this wallet?", "which yield strategy for 0x...", "best DeFi products for this wallet", "personalise DeFi recommendations"
**Input:** wallet address + network
**Output:** Recommended product tier, specific DeFi products, rationale based on experience + risk profile

---

### chainaware-airdrop-screener
**File:** `.claude/agents/chainaware-airdrop-screener.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`, `predictive_behaviour`
**Purpose:** Batch screens wallets for airdrop eligibility; filters bots and fraud; ranks eligible wallets by reputation score.
**Triggers:** "screen these wallets for our airdrop", "filter bots from this list", "sybil filter for airdrop", "fair airdrop allocation", "remove fake wallets"
**Input:** list of wallet addresses + network. Optional: fraud threshold, token budget
**Output:** Eligible ranked list, flagged list, disqualified list, token allocations, cohort breakdown

---

### chainaware-lending-risk-assessor
**File:** `.claude/agents/chainaware-lending-risk-assessor.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`, `predictive_behaviour`
**Purpose:** Assesses borrower risk for DeFi lending protocols. Returns risk grade, collateral ratio, and interest rate tier.
**Triggers:** "what collateral should I require from 0x...", "lending risk for this address", "what LTV for this wallet?", "assess this borrower", "should I lend to this wallet?"
**Input:** wallet address + network. Optional: loan amount, asset type, platform risk policy
**Output:** Risk grade (A–F), recommended collateral ratio (%), interest rate tier, risk breakdown

---

### chainaware-token-launch-auditor
**File:** `.claude/agents/chainaware-token-launch-auditor.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_rug_pull`, `predictive_fraud`, `predictive_behaviour`
**Purpose:** Pre-listing launch safety audit combining contract rug pull risk and deployer wallet screening.
**Triggers:** "should we list this token?", "audit this launch", "is this deployer trustworthy?", "vet this IDO", "launch safety check", "pre-listing safety check"
**Input:** token contract address + deployer wallet address + network
**Output:** Launch Safety Score, verdict (APPROVED / CONDITIONAL / REJECTED), safety badge, listing conditions

---

### chainaware-agent-screener
**File:** `.claude/agents/chainaware-agent-screener.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`, `predictive_behaviour`
**Purpose:** Screens an AI agent's operational wallet and feeder wallet for trustworthiness.
**Formula:** Agent Trust Score 0–10 (0=fraud, 1=new/insufficient data, 2–10=normalised reputation)
**Triggers:** "is this agent wallet safe?", "screen this agent", "check the feeder wallet for this agent", "can I trust this agent?", "agent trust score for 0x..."
**Input:** agent wallet address + feeder wallet address + network
**Output:** Agent Trust Score (0–10), per-wallet fraud verdict, overall recommendation

---

### chainaware-cohort-analyzer
**File:** `.claude/agents/chainaware-cohort-analyzer.md`
**Model:** claude-sonnet-4-6
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Segments a batch of wallets into behavioural cohorts with per-cohort engagement strategies.
**Cohorts:** Power DeFi User, NFT Collector, Yield Farmer, Multi-Chain Explorer, Active Trader, Casual User, Dormant/Inactive, New/Fresh, Unclassified, Excluded (fraud/bot)
**Triggers:** "segment these wallets", "who are my power users?", "cohort analysis for these addresses", "what types of users do I have?", "behavioral mix of my community"
**Input:** list of wallet addresses + network. Optional: engagement goal, custom cohort labels
**Output:** Cohort distribution table, wallet-level detail, per-cohort engagement playbook, audience quality score

---

### chainaware-counterparty-screener
**File:** `.claude/agents/chainaware-counterparty-screener.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`, `predictive_behaviour`
**Purpose:** Real-time pre-transaction safety check. Returns a go/no-go verdict before a trade, transfer, or contract interaction.
**Verdicts:** 🟢 Safe / 🟡 Caution / 🔴 Block
**Triggers:** "is it safe to send to this address?", "check this counterparty", "pre-transaction check", "quick safety check on 0x...", "should I trade with this wallet?"
**Input:** counterparty wallet address + network. Optional: transaction type (transfer / trade / contract / LP deposit)
**Output:** Verdict (Safe / Caution / Block), one-line reason, key signals, recommended action

---

### chainaware-upsell-advisor
**File:** `.claude/agents/chainaware-upsell-advisor.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Identifies the best upsell opportunity for an existing user. Scores upgrade readiness (0–100), recommends the specific next product, calculates conversion probability, identifies the optimal trigger event, and generates a ready-to-use upsell message.
**Triggers:** "what should I upsell to 0x...", "next product for this user", "is this wallet ready to upgrade?", "upgrade path for this wallet", "when should I offer the next tier?", "best upsell for this wallet", "next-best-product for 0x...", "upgrade readiness check"
**Input:** wallet address + network + current product/tier. Optional: product catalogue, upsell goal (revenue / engagement / retention)
**Output:** Upgrade readiness score (0–100), conversion probability, recommended next product, trigger event, ready-to-use upsell message, "what NOT to do" warning. Batch mode ranks full user lists by upsell readiness.

---

### chainaware-lead-scorer
**File:** `.claude/agents/chainaware-lead-scorer.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** Sales lead qualification engine. Scores a wallet 0–100 as a conversion prospect, assigns a lead tier, estimates conversion probability, and recommends a specific outreach angle.
**Tiers:** 🔥 Hot (75–100) / 🟡 Warm (50–74) / 🔵 Cold (25–49) / ⚫ Dead (0–24 or disqualified)
**Triggers:** "is this a good lead?", "score this wallet as a prospect", "lead quality for this address", "which wallets are worth pursuing?", "hot leads in this list", "sales qualification for this address", "conversion potential for 0x...", "rank these wallets by sales potential"
**Input:** wallet address + network. Optional: product context, outreach goal (acquisition / upsell / reactivation), batch list
**Output:** Lead score (0–100), tier, conversion probability, outreach angle, channel fit, timing signal, batch ranked list

---

### chainaware-transaction-monitor
**File:** `.claude/agents/chainaware-transaction-monitor.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_fraud`, `predictive_rug_pull`, `predictive_behaviour`
**Purpose:** Real-time transaction risk scoring for autonomous AI agents and automated pipelines. Screens sender, receiver, and contract; returns a composite risk score (0–100) and a machine-actionable pipeline action.
**Actions:** ✅ ALLOW / ⚠️ FLAG / 🔶 HOLD / 🛑 BLOCK
**Triggers:** "should my agent execute this transaction?", "risk score for this tx", "monitor this transaction", "flag or allow this transfer?", "pipeline risk signal for this event", "autonomous transaction screening", "compliance check for this transaction"
**Input:** sender address + receiver address + network. Optional: contract address, transaction value, action type (transfer / swap / stake / bridge / mint / approve / liquidity)
**Output:** Composite risk score (0–100), per-address risk levels, pipeline action, operator note. Compact mode available for programmatic consumption.

---

### chainaware-governance-screener
**File:** `.claude/agents/chainaware-governance-screener.md`
**Model:** claude-haiku-4-5-20251001
**Tools:** `predictive_behaviour`, `predictive_fraud`
**Purpose:** DAO voter screening — Sybil detection, governance tier classification, and voting weight multiplier calculation.
**Tiers:** Core Contributor (2×) / Active Member (1.5×) / Participant (1×) / Observer (0.5×) / Disqualified (0×)
**Governance models supported:** Token-weighted, reputation-weighted, quadratic
**Triggers:** "should this wallet be allowed to vote?", "voting weight for 0x...", "screen our DAO members", "Sybil check for governance", "filter fake voters", "governance health check"
**Input:** wallet address (or list) + network. Optional: governance model, voting power pool, participation threshold
**Output:** Governance tier, voting weight multiplier, Sybil risk verdict, batch leaderboard, governance health score

---

## Network Support Matrix

| Tool | ETH | BNB | POLYGON | TON | BASE | TRON | HAQQ | SOLANA |
|------|-----|-----|---------|-----|------|------|------|--------|
| `predictive_fraud` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | — |
| `predictive_behaviour` | ✅ | ✅ | — | — | ✅ | — | ✅ | ✅ |
| `predictive_rug_pull` | ✅ | ✅ | — | — | ✅ | — | ✅ | — |
| `token_rank_list` | ✅ | ✅ | — | — | ✅ | — | — | ✅ |
| `token_rank_single` | ✅ | ✅ | — | — | ✅ | — | — | ✅ |

## Composability Map

```
Autonomous transaction pipeline  → chainaware-transaction-monitor
Transaction safety check         → chainaware-counterparty-screener
  └─ needs more detail       → chainaware-analyst
  └─ is a contract address   → chainaware-rug-pull-detector

User onboarding              → chainaware-onboarding-router
  └─ personalise DeFi offer  → chainaware-defi-advisor
  └─ personalise message     → chainaware-wallet-marketer

Airdrop campaign             → chainaware-airdrop-screener
  └─ whale tier bonuses      → chainaware-whale-detector
  └─ message each recipient  → chainaware-wallet-marketer

DAO governance vote          → chainaware-governance-screener
  └─ full member audit       → chainaware-analyst
  └─ AML on flagged wallets  → chainaware-aml-scorer

DeFi lending                 → chainaware-lending-risk-assessor
  └─ full borrower profile   → chainaware-analyst

Token / launchpad vetting    → chainaware-token-launch-auditor
  └─ token holder quality    → chainaware-token-analyzer
  └─ deployer deep dive      → chainaware-analyst

User analytics               → chainaware-cohort-analyzer
  └─ per-cohort messaging    → chainaware-wallet-marketer
  └─ onboard new cohort      → chainaware-onboarding-router

AI agent verification        → chainaware-agent-screener
  └─ full agent audit        → chainaware-analyst
```

## Further Reading

- Full documentation: https://github.com/ChainAware/behavioral-prediction-mcp
- MCP integration guide: https://chainaware.ai/blog/prediction-mcp-for-ai-agents-personalize-decisions-from-wallet-behavior/
- 12 agent capabilities: https://chainaware.ai/blog/12-blockchain-capabilities-any-ai-agent-can-use-mcp-integration-guide/
- Complete product guide: https://chainaware.ai/blog/chainaware-ai-products-complete-guide/
- API access: https://chainaware.ai/pricing
