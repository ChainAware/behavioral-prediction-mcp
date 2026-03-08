# ChainAware Behavioral Prediction MCP

## Project Overview

This repository contains the **ChainAware Behavioral Prediction MCP** — an AI-native Web3 intelligence layer that gives AI agents predictive capabilities over blockchain wallets and smart contracts.

- **MCP Endpoint:** `https://prediction.mcp.chainaware.ai/sse`
- **API Key:** Set as `CHAINAWARE_API_KEY` environment variable (never hardcode)
- **GitHub:** `https://github.com/ChainAware/behavioral-prediction-mcp`
- **Coverage:** 14M+ wallets, 8 blockchains, 1.3B+ data points

---

## MCP Tools (5 total)

| Tool | Purpose | Networks |
|---|---|---|
| `predictive_fraud` | Fraud probability + AML forensics for a wallet | ETH, BNB, POLYGON, TON, BASE, TRON, HAQQ |
| `predictive_behaviour` | Wallet segmentation, intent, experience, recommendations | ETH, BNB, BASE, HAQQ, SOLANA |
| `predictive_rug_pull` | Smart contract rug pull risk scoring | ETH, BNB, BASE, HAQQ |
| `token_rank_list` | Ranked list of tokens by holder community strength | ETH, BNB, BASE, SOLANA |
| `token_rank_single` | Token rank + top holders for a specific contract | ETH, BNB, BASE, SOLANA |

---

## Repository Structure

```
behavioral-prediction-mcp/
├── .claude/
│   └── agents/              # 20 Claude Code subagents
├── agents/
│   └── openai.yaml          # Codex/OpenAI metadata
├── references/              # Deep tool documentation
│   ├── tools-fraud.md
│   ├── tools-behaviour.md
│   ├── tools-rug-pull.md
│   ├── tools-token-rank-list.md
│   └── tools-token-rank-single.md
├── CLAUDE.md                # This file
├── SKILL.md                 # Skill definition (Claude, Codex, Cursor)
├── SUBAGENT-IDEAS.md        # Backlog of subagent ideas with priorities
└── README.md
```

---

## Subagents

20 specialist subagents in `.claude/agents/`. Use the right one for the task:

| Agent | Model | Tools Used | Use For |
|---|---|---|---|
| `chainaware-analyst` | Sonnet | All 3 prediction tools | Full due diligence, complex analysis |
| `chainaware-fraud-detector` | Haiku | `predictive_fraud` | Fast fraud screening, batch checks |
| `chainaware-rug-pull-detector` | Haiku | `predictive_rug_pull` + `predictive_fraud` | Contract/LP safety checks |
| `chainaware-wallet-marketer` | Sonnet | `predictive_behaviour` + `predictive_fraud` | Personalized marketing messages |
| `chainaware-reputation-scorer` | Haiku | `predictive_behaviour` + `predictive_fraud` | Reputation score 0–4000 |
| `chainaware-aml-scorer` | Haiku | `predictive_fraud` | AML compliance scoring 0–100 |
| `chainaware-trust-scorer` | Haiku | `predictive_fraud` | Simple trust score 0.00–1.00 |
| `chainaware-wallet-ranker` | Haiku | `predictive_behaviour` | Wallet experience rank + leaderboard |
| `chainaware-token-ranker` | Haiku | `token_rank_list` | Discover/rank tokens by holder community strength |
| `chainaware-token-analyzer` | Haiku | `token_rank_single` + `predictive_fraud` | Single token deep-dive + top holders |
| `chainaware-onboarding-router` | Haiku | `predictive_behaviour` + `predictive_fraud` | Route wallets to beginner/intermediate/skip onboarding |
| `chainaware-whale-detector` | Haiku | `predictive_behaviour` + `predictive_fraud` | Classify wallets into whale tiers (Mega/Whale/Emerging) |
| `chainaware-defi-advisor` | Haiku | `predictive_behaviour` + `predictive_fraud` | Personalized DeFi product recommendations by experience + risk tier |
| `chainaware-airdrop-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | Batch screen wallets for airdrop eligibility, filter bots/fraud, rank by reputation |
| `chainaware-lending-risk-assessor` | Haiku | `predictive_fraud` + `predictive_behaviour` | Borrower risk grade (A–F), collateral ratio, and interest rate tier for DeFi lending |
| `chainaware-token-launch-auditor` | Haiku | `predictive_rug_pull` + `predictive_fraud` + `predictive_behaviour` | Pre-listing launch safety audit — composite LSS, APPROVED/CONDITIONAL/REJECTED verdict, badge copy |
| `chainaware-agent-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | Screens AI agent wallet + feeder wallet; returns Agent Trust Score 0–10 (0=fraud, 1=new, 2–10=reputation) |
| `chainaware-cohort-analyzer` | Sonnet | `predictive_behaviour` + `predictive_fraud` | Segments a batch of wallets into behavioral cohorts (Power DeFi, NFT, Trader, Dormant, etc.) with per-cohort engagement strategies |
| `chainaware-counterparty-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | Real-time pre-transaction go/no-go verdict (Safe / Caution / Block) before a trade, transfer, or contract interaction |
| `chainaware-governance-screener` | Haiku | `predictive_behaviour` + `predictive_fraud` | DAO voter screening — Sybil detection, governance tier (Core/Active/Participant/Observer), and voting weight multiplier |

### Key Scoring Formulas

**Reputation Score:** `1000 × (experience + 1) × (willingness_to_take_risk + 1) × (1 - fraud_probability)`

**AML Score:** `(1 - probabilityFraud) × 100` (only when forensic_details has no negative flags; else score = 0)

**Trust Score:** `1 - probabilityFraud` → returns 0.00–1.00

---

## Conventions

- **Haiku** for single-purpose, fast, deterministic agents
- **Sonnet** for agents requiring reasoning, creativity, or multi-tool orchestration
- Subagents should **escalate** to `chainaware-analyst` when a task exceeds their scope
- Never hardcode API keys — always use `CHAINAWARE_API_KEY` env var
- Token rank tools (`token_rank_list`, `token_rank_single`) are documented in SKILL.md and have reference docs
- SKILL.md includes OpenClaw metadata (`version`, `metadata.openclaw`) for ClawHub publishing

---

## Blog Articles & Reference Reading

### Product Overviews
- [ChainAware Complete Product Guide](https://chainaware.ai/blog/chainaware-ai-products-complete-guide/) — Overview of all tools, networks, and what ChainAware does
- [Web3 Business Potential](https://chainaware.ai/blog/web3-business-potential/) — Business case and market opportunity for Web3 intelligence
- [Use ChainAware as a Business](https://chainaware.ai/blog/use-chainaware-as-business/) — How to build commercial products and services on top of ChainAware

### Tool-Specific Guides
- [Fraud Detector Guide](https://chainaware.ai/blog/chainaware-fraud-detector-guide/) — How to use `predictive_fraud`: inputs, outputs, thresholds, use cases
- [Rug Pull Detector Guide](https://chainaware.ai/blog/chainaware-rugpull-detector-guide/) — How to use `predictive_rug_pull`: contract scoring, deployer risk, LP analysis
- [Token Rank Guide](https://chainaware.ai/blog/chainaware-token-rank-guide/) — How to use `token_rank_list` and `token_rank_single`: community strength scoring
- [Wallet Rank Guide](https://chainaware.ai/blog/chainaware-wallet-rank-guide/) — Wallet ranking system: experience tiers, global rank, points
- [Wallet Auditor Guide](https://chainaware.ai/blog/chainaware-wallet-auditor-how-to-use/) — Full wallet audit workflow combining multiple tools
- [Transaction Monitoring Guide](https://chainaware.ai/blog/chainaware-transaction-monitoring-guide/) — Real-time transaction risk monitoring patterns
- [Web3 Behavioral User Analytics Guide](https://chainaware.ai/blog/chainaware-web3-behavioral-user-analytics-guide/) — Using `predictive_behaviour` for user analytics and segmentation
- [Credit Score Guide](https://chainaware.ai/blog/chainaware-credit-score-the-complete-guide-to-web3-credit-scoring-in-2026/) — Web3 credit scoring methodology and use in DeFi lending

### Analytics & Strategy
- [Web3 User Segmentation & Behavioral Analytics for DApp Growth](https://chainaware.ai/blog/web3-user-segmentation-behavioral-analytics-for-dapp-growth-2026/) — Segmentation strategies for DApp retention and growth
- [AI-Powered Blockchain Analysis: Machine Learning for Crypto Security](https://chainaware.ai/blog/ai-powered-blockchain-analysis-machine-learning-for-crypto-security-2026/) — ML approaches to on-chain security and fraud detection
- [Forensic Crypto Analytics vs AI-Based Crypto Analytics](https://chainaware.ai/blog/forensic-crypto-analytics-versus-ai-based-crypto-analytics/) — Comparison of traditional forensic tools vs ChainAware's predictive AI approach

### Developer Integration
- [12 Blockchain Capabilities Any AI Agent Can Use — MCP Integration Guide](https://chainaware.ai/blog/12-blockchain-capabilities-any-ai-agent-can-use-mcp-integration-guide/) — All 12 agent capabilities explained with MCP setup for Claude, ChatGPT, Cursor, and multi-agent architectures
- [Prediction MCP for AI Agents: Personalize Decisions from Wallet Behavior](https://chainaware.ai/blog/prediction-mcp-for-ai-agents-personalize-decisions-from-wallet-behavior/) — Full MCP integration guide with code examples
- [Top 5 Ways Prediction MCP Will Turbocharge Your DeFi Platform](https://chainaware.ai/blog/top-5-ways-prediction-mcp-will-turbocharge-your-defi-platform/) — Lending, DEX, launchpad, governance, and personalization use cases
- [Why Personalization Is the Next Big Thing for AI Agents](https://chainaware.ai/blog/why-personalization-is-the-next-big-thing-for-ai-agents/) — The case for wallet-level personalization in Web3
- [DeFi Onboarding in 2026: Why 90% of Connected Wallets Never Transact and How AI Agents Fix It](https://chainaware.ai/blog/defi-onboarding-in-2026-why-90-of-connected-wallets-never-transact-and-how-ai-agents-fix-it/) — Onboarding conversion problem and AI-driven solutions
- [The Web3 Agentic Economy: How AI Agents Are Replacing Human Teams in DeFi](https://chainaware.ai/blog/the-web3-agentic-economy-how-ai-agents-are-replacing-human-teams-in-defi/) — How autonomous AI agents are taking over DeFi operations and decision-making

### Tool Reference Docs (this repo)
- `references/tools-fraud.md` — `predictive_fraud` full schema, thresholds, forensic flags
- `references/tools-behaviour.md` — `predictive_behaviour` full schema, intent signals, personalization patterns
- `references/tools-rug-pull.md` — `predictive_rug_pull` full schema, 3-layer scoring model
- `references/tools-token-rank-list.md` — `token_rank_list` full schema, categories, sorting, pagination
- `references/tools-token-rank-single.md` — `token_rank_single` full schema, top holders, global rank

---

## Next Tasks (from SUBAGENT-IDEAS.md)

All high-priority subagents have been built. See SUBAGENT-IDEAS.md for future ideas.

Also pending:
- Publish skill to OpenClaw ClawHub (`clawhub publish`)
- Submit to OpenAI skill registry
