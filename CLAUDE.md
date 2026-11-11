# ChainAware Behavioral Prediction MCP

## Project Overview

This repository contains the **ChainAware Behavioral Prediction MCP** — an AI-native Web3 intelligence layer that gives AI agents predictive capabilities over blockchain wallets and smart contracts.

- **MCP Endpoint:** `https://prediction.mcp.chainaware.ai/sse`
- **API Key:** Set as `CHAINAWARE_API_KEY` environment variable (never hardcode) · x402 payment also supported
- **GitHub:** `https://github.com/ChainAware/behavioral-prediction-mcp`
- **Coverage:** 14M+ wallets, 8 blockchains, 1.3B+ data points
- **Website:** https://chainaware.ai
- **Twitter:** https://x.com/ChainAware/
- **LinkedIn:** https://www.linkedin.com/company/chainaware
- **Blog:** https://chainaware.ai/blog
- **Learn:** https://chainaware.ai/learn
- **Examples:** https://github.com/ChainAware/examples
- **Fraud Detection Accuracy:** [98% backtesting verified](https://chainaware.ai/scam-db)
- **Rug Pull Detection Accuracy:** [90.1% backtesting verified](https://chainaware.ai/resources/rugpull-verification)
- **Featured in:** [CB Insights Fraud Prevention Market Map for the AI Era](https://www.cbinsights.com/research/report/the-fraud-prevention-market-map-for-the-ai-era/) (2026)
- **Listed on:** [BNB Chain AI Landscape](https://x.com/BNBCHAIN/status/1947597551139500419) (2025)
- **Listed on:** [BNB Chain Kickstart — Marketing Tools](https://www.bnbchain.org/en/programs/kickstart#services) (2025)
- **Awarded:** [Google Cloud $250k Grant](https://chainaware.ai/blog/google-cloud-grant/) (2025)
- **Selected for:** [AWS Fintech Accelerator](https://chainaware.ai/blog/aws-grant/) (2024)
- **Listed on:** [Safary Club Web3 Growth Landscape — Growth Tools](https://x.com/Safaryclub/status/1822983239734329613) (2024)

---

## MCP Tools (14 total)

| Tool | Purpose | Networks |
|---|---|---|
| `predictive_fraud` | Fraud probability + AML forensics for a wallet | ETH, BNB, POLYGON, TON, BASE, TRON, HAQQ |
| `predictive_fraud_batch` | Schedule batch fraud detection for a list of wallets; returns job_id + signature | ETH, BNB, POLYGON, TON, BASE, TRON, HAQQ |
| `predictive_behaviour` | Wallet segmentation, intent, experience, recommendations | ETH, BNB, BASE, HAQQ, SOLANA |
| `predictive_behaviour_batch` | Schedule batch behavioural analysis for a list of wallets; returns job_id + signature | ETH, BNB, BASE, HAQQ, SOLANA |
| `check_job_status` | Poll progress of a batch job (completed / failed / pending counts) | — |
| `get_job_results` | Retrieve full per-wallet results from a completed or partial batch job | — |
| `predictive_rug_pull` | Smart contract rug pull risk scoring | ETH, BNB, BASE, HAQQ |
| `credit_score` | Crypto credit/trust score (1–9) combining fraud + social graph analysis | ETH |
| `token_rank_list` | Ranked list of tokens by holder community strength | ETH, BNB, BASE, SOLANA |
| `token_rank_single` | Token rank + top holders for a specific contract | ETH, BNB, BASE, SOLANA |
| `run_token_audit` | Deep multi-module token contract audit (get-or-create; returns full report if cached or queues job) | eth, bsc, base, arbitrum, avalanche, optimism, polygon |
| `get_token_audit_result` | Poll/retrieve completed token audit — 8 modules, 0–100 risk score, honeypot analysis | eth, bsc, base, arbitrum, avalanche, optimism, polygon |
| `agents_trust_score_list` | Paginated list of ERC-8004 registered AI agents with trust scores 0–1000 | — |
| `agents_trust_score_single` | Deep trust profile for a single ERC-8004 agent by agent_id + chain_id | — |

### Batch Pipeline

The four batch tools form a sequential pipeline:
```
predictive_fraud_batch / predictive_behaviour_batch → check_job_status → get_job_results
```
Always store `job_id` + `signature` from the schedule call — both are required for all follow-up calls. Do not call `get_job_results` until `check_job_status` returns `completed` or `partial`.

---

## Repository Structure

```
behavioral-prediction-mcp/
├── .claude/
│   └── agents/              # 34 Claude Code subagents
├── agents/
│   └── openai.yaml          # Codex/OpenAI metadata
├── references/              # Deep tool documentation
│   ├── tools-fraud.md
│   ├── tools-behaviour.md
│   ├── tools-batch.md
│   ├── tools-rug-pull.md
│   ├── tools-credit-score.md
│   ├── tools-token-rank-list.md
│   └── tools-token-rank-single.md
├── CLAUDE.md                # This file
├── SKILL.md                 # Skill definition (Claude, Codex, Cursor)
└── README.md
```

---

## Subagents

34 specialist subagents in `.claude/agents/`. Use the right one for the task:

| Agent | Model | Tools Used | Use For |
|---|---|---|---|
| `chainaware-wallet-auditor` | Sonnet | `predictive_behaviour` + `WebFetch` + `WebSearch` | Full due diligence, complex analysis |
| `chainaware-fraud-detector` | Sonnet | `predictive_fraud` + batch tools + `WebSearch` | Fast fraud screening, batch checks |
| `chainaware-rug-pull-detector` | Haiku | `predictive_rug_pull` + `WebSearch` | Contract/LP safety checks |
| `chainaware-wallet-marketer` | Sonnet | `predictive_behaviour` | Personalized marketing messages |
| `chainaware-reputation-scorer` | Haiku | `predictive_behaviour` | Reputation score 0–1000 |
| `chainaware-aml-scorer` | Haiku | `predictive_fraud` | AML compliance scoring 0–100 |
| `chainaware-trust-scorer` | Haiku | `predictive_fraud` | Simple trust score 0.00–1.00 |
| `chainaware-credit-scorer` | Haiku | `credit_score` | Crypto credit score 1–9 combining fraud probability + social graph analysis |
| `chainaware-ltv-estimator` | Haiku | `predictive_behaviour` + `predictive_fraud` | 12-month revenue potential (LTV) as a USD range — models tx count × avg tx value × fee rate, scaled by categories, risk, and retention. Optional: platform_share (default 0.15), fee_rate (default 0.001) |
| `chainaware-wallet-ranker` | Haiku | `predictive_behaviour` | Wallet experience rank + leaderboard |
| `chainaware-token-ranker` | Haiku | `token_rank_list` | Discover/rank tokens by holder community strength |
| `chainaware-token-analyzer` | Haiku | `token_rank_single` + `predictive_fraud` | Single token deep-dive + top holders |
| `chainaware-onboarding-router` | Haiku | `predictive_behaviour` | Route wallets to beginner/intermediate/skip onboarding |
| `chainaware-whale-detector` | Haiku | `predictive_behaviour` + batch tools | Classify wallets into whale tiers (Mega/Whale/Emerging) |
| `chainaware-defi-advisor` | Haiku | `predictive_behaviour` | Personalized DeFi product recommendations by experience + risk tier |
| `chainaware-airdrop-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | Batch screen wallets for airdrop eligibility, filter bots/fraud, rank by reputation |
| `chainaware-lending-risk-assessor` | Haiku | `predictive_fraud` + `predictive_behaviour` + `credit_score` | Borrower risk grade (A–F), collateral ratio, and interest rate tier for DeFi lending |
| `chainaware-token-launch-auditor` | Haiku | `predictive_rug_pull` + `predictive_fraud` + `predictive_behaviour` | Pre-listing launch safety audit — composite LSS, APPROVED/CONDITIONAL/REJECTED verdict, badge copy |
| `chainaware-agent-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` + `predictive_rug_pull` | Screens AI agent wallet + feeder wallet; returns Agent Trust Score 0–10 (0=fraud, 1=new, 2–10=reputation) |
| `chainaware-cohort-analyzer` | Sonnet | `predictive_behaviour` + `predictive_fraud` | Segments a batch of wallets into behavioral cohorts (Power DeFi, NFT, Trader, Dormant, etc.) with per-cohort engagement strategies |
| `chainaware-counterparty-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | Real-time pre-transaction go/no-go verdict (Safe / Caution / Block) before a trade, transfer, or contract interaction |
| `chainaware-governance-screener` | Haiku | `predictive_behaviour` + `predictive_fraud` | DAO voter screening — Sybil detection, governance tier (Core/Active/Participant/Observer), and voting weight multiplier |
| `chainaware-sybil-detector` | Haiku | `predictive_behaviour` + `predictive_fraud` | Bulk Sybil attack detection for DAO votes — classifies voters as ELIGIBLE / REVIEW / EXCLUDE, detects coordinated fraud patterns, and produces reputation-weighted vote multipliers |
| `chainaware-transaction-monitor` | Haiku | `predictive_fraud` + `predictive_rug_pull` + `predictive_behaviour` | Real-time transaction risk scoring for autonomous agents — composite score (0–100), per-address fraud signals, and pipeline action (ALLOW / FLAG / HOLD / BLOCK) |
| `chainaware-lead-scorer` | Haiku | `predictive_behaviour` + `predictive_fraud` | Sales lead qualification — lead score (0–100), tier (Hot/Warm/Cold/Dead), conversion probability, and recommended outreach angle |
| `chainaware-upsell-advisor` | Haiku | `predictive_behaviour` | Upsell path for existing users — upgrade readiness score, next product recommendation, conversion probability, trigger event, and ready-to-use upsell message |
| `chainaware-platform-greeter` | Haiku | `predictive_behaviour` + `predictive_fraud` | Contextual welcome message for a specific wallet on a specific platform — same wallet gets a different message on Aave vs 1inch vs OpenSea |
| `chainaware-marketing-director` | Sonnet | `Agent` + `predictive_fraud` (orchestrator) | Full-cycle campaign orchestrator — segments audience, scores leads, detects whales, builds per-cohort message playbook, surfaces upsell opportunities, and routes new wallets; all messages tailored to a caller-supplied platform description |
| `chainaware-compliance-screener` | Haiku | `Agent` + `predictive_fraud` + `predictive_rug_pull` (orchestrator) | First-layer MiCA-aligned compliance screening — orchestrates fraud-detector, aml-scorer, transaction-monitor, and counterparty-screener into a structured Compliance Report with verdict (PASS / EDD / REJECT), risk rating, and explicit scope disclaimer (~70–75% MiCA coverage for pure DeFi) |
| `chainaware-gamefi-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | Web3 game and P2E platform wallet screening — detects bot farms, cheaters, and farm wallets; classifies legitimate players into experience tiers (Casual/Active/Veteran/Pro) for matchmaking; outputs P2E reward eligibility and multiplier |
| `chainaware-portfolio-risk-advisor` | Sonnet | `predictive_rug_pull` + `token_rank_single` | Portfolio-level rug pull and community health assessment — scans every token via predictive_rug_pull (universal), enriches with token_rank_single where available (2,500–3,000 token index), produces weighted Portfolio Risk Score, grade (A–F), concentration flags, and prioritized rebalancing plan |
| `chainaware-rwa-investor-screener` | Haiku | `predictive_fraud` + `predictive_behaviour` | RWA investor suitability screening — assesses fraud risk, on-chain experience, and risk profile alignment against the RWA tier; returns QUALIFIED / CONDITIONAL / REFER_TO_KYC / DISQUALIFIED with recommended investment cap |
| `chainaware-agent-trust-screener` | Haiku | `agents_trust_score_list` + `agents_trust_score_single` | ERC-8004 agent discovery and trust screening — lists/ranks registered AI agents by 0–1000 trust score, returns trust_tier and trust_flags, flags wallet_verified=false as a hard warning |
| `chainaware-token-audit-analyst` | Haiku | `run_token_audit` + `get_token_audit_result` | Deep multi-module token contract audit — ownership control, liquidity health, supply/mint risk, honeypot detection, reentrancy; async get-or-create pipeline; returns 0–100 risk score with module breakdown and verdict (APPROVED / CONDITIONAL / REJECTED) |

### Key Scoring Formulas

**Reputation Score:** `(1000 / 110) × (experience + 1) × (riskCapability + 1) × (1 - probabilityFraud)` (max = 1000; `experience` raw 0–10, `riskCapability` raw 0–9 — both direct fields from `predictive_behaviour`)

**AML Score:** `(1 - probabilityFraud) × 100` (only when forensic_details has no negative flags; else score = 0)

**Trust Score:** `1 - probabilityFraud` → returns 0.00–1.00

---

## Conventions

- **Haiku** for single-purpose, fast, deterministic agents
- **Sonnet** for agents requiring reasoning, creativity, or multi-tool orchestration
- Subagents should **escalate** to `chainaware-wallet-auditor` when a task exceeds their scope
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
- [Credit Scoring Agent Guide](https://chainaware.ai/blog/chainaware-credit-scoring-agent-guide/) — How to build and use the ChainAware credit scoring agent
- [AI Agent Trust Score](https://chainaware.ai/blog/agent-trust-score-agentic-commerce/) — `agents_trust_score_list` / `agents_trust_score_single`: ERC-8004 registry, 274k+ agents, trust tiers 0–1000, fleet-level fraud detection, DeFi access control
- [Token Audit: 10,000 CoinGecko Token Results](https://chainaware.ai/blog/token-audit-10000-coingecko-results/) — Real-world study: 55% of top tokens HIGH RISK; 127 automated checks via `run_token_audit`
- [Token Audit at Scale: 15,000 EVM Tokens](https://chainaware.ai/blog/token-audit-15000-coingecko-coinmarketcap-2026/) — Larger 2026 study: 57.7% HIGH RISK across CoinGecko + CoinMarketCap tokens; `run_token_audit` accuracy benchmark
- [$284M+ Rug Pulls on PancakeSwap V2](https://chainaware.ai/blog/rugpull-detector-v3-pancakev2-2026/) — Real-world `predictive_rug_pull` accuracy: 103,695 rug pull events across 20 weeks; 90.1% detection rate verified
- [AML and Transaction Monitoring for DApps](https://chainaware.ai/blog/how-to-integrate-ai-based-aml-transaction-monitoring-dapps/) — `aml_screener` integration patterns; VASP compliance, real-time screening, alert thresholds
- [Wallet Forensics and AML Investigation Reports](https://chainaware.ai/blog/chainaware-investigation-wallet-forensics-aml-reports/) — Deep investigation workflow combining `predictive_fraud` + AML + behavioural scoring into structured compliance reports
- [Share My Audit / Web3 Business Card](https://chainaware.ai/blog/chainaware-share-my-audit-guide/) — Consumer use case for `predictive_behaviour`: shareable wallet identity profile, trust passport

### Analytics & Strategy
- [Web3 User Segmentation & Behavioral Analytics for DApp Growth](https://chainaware.ai/blog/web3-user-segmentation-behavioral-analytics-for-dapp-growth-2026/) — Segmentation strategies for DApp retention and growth
- [AI-Powered Blockchain Analysis: Machine Learning for Crypto Security](https://chainaware.ai/blog/ai-powered-blockchain-analysis-machine-learning-for-crypto-security-2026/) — ML approaches to on-chain security and fraud detection
- [Forensic Crypto Analytics vs AI-Based Crypto Analytics](https://chainaware.ai/blog/forensic-crypto-analytics-versus-ai-based-crypto-analytics/) — Comparison of traditional forensic tools vs ChainAware's predictive AI approach
- [MiCA Compliance DeFi Screener](https://chainaware.ai/blog/mica-compliance-defi-screener-chainaware/) — How ChainAware covers ~70–75% of DeFi MiCA obligations at pay-per-use pricing; positions compliance-screener vs Chainalysis/Elliptic; Travel Rule does not apply to pure DeFi
- [Web3 Growth Platforms Compared 2026](https://chainaware.ai/blog/web3-growth-platforms-compared-2026/) — Three-stage funnel framework (Find / Understand / Convert); ChainAware is the only platform operating at Stage 3 (post-connection, in-DApp personalization); relevant to onboarding-router, wallet-marketer, whale-detector, marketing-director
- [Web3 Analytics Tools for DApps Comparison 2026](https://chainaware.ai/blog/web3-analytics-tools-dapps-comparison-2026/) — Maps 10 analytics platforms across four job categories; ChainAware is the sole "predictive intelligence" platform vs attribution/product analytics tools; covers behavioral analytics, fraud detector, growth agents
- [Web3 Fraud Detection for DApps](https://chainaware.ai/blog/web3-fraud-detection-for-dapps/) — `predictive_fraud` competitive positioning: why behavioral scoring beats transaction-pattern detection; 98% accuracy benchmark
- [Web3 Rug Pull Detection Tools Compared](https://chainaware.ai/blog/best-web3-rug-pull-detection-tools-2026/) — `predictive_rug_pull` vs GoPlus, De.Fi, Token Sniffer; why behavioral holder analysis catches what code audits miss
- [DeFi Credit Score Platforms Compared](https://chainaware.ai/blog/defi-credit-score-comparison/) — `credit_score` vs Providence, RociFi, TrueFi, Masa, Spectral; ChainAware is only platform integrating fraud probability into credit scoring
- [DeFi Compliance Tools Comparison](https://chainaware.ai/blog/defi-compliance-tools-protocols-comparison-2026/) — `chainaware-compliance-screener` vs Chainalysis, TRM Labs, Elliptic; 118 organic sessions/mo, highest-traffic comparison post
- [Web3 Reputation Score Comparison](https://chainaware.ai/blog/web3-reputation-score-comparison-2026/) — ChainAware vs Nomis, RubyScore, Ethos, Trusta Labs; positions `predictive_behaviour` reputation scoring
- [Blockchain Data Providers for AI Agents](https://chainaware.ai/blog/blockchain-data-providers-ai-agents-wallet-data-2026/) — Where ChainAware (Tier 2: pre-computed intelligence) sits vs Moralis, Nansen, Dune, Alchemy (Tier 1: raw indexed data)
- [ChainAware's 32 Claude Sub-Agents](https://chainaware.ai/blog/chainaware-32-claude-sub-agents-fraud-tech-growth-tech-agentic-economy/) — Full catalogue of all 32 sub-agents, their tools, scoring formulas, and use cases

### Developer Integration
- [12 Blockchain Capabilities Any AI Agent Can Use — MCP Integration Guide](https://chainaware.ai/blog/12-blockchain-capabilities-any-ai-agent-can-use-mcp-integration-guide/) — All 12 agent capabilities explained with MCP setup for Claude, ChatGPT, Cursor, and multi-agent architectures
- [Prediction MCP for AI Agents: Personalize Decisions from Wallet Behavior](https://chainaware.ai/blog/prediction-mcp-for-ai-agents-personalize-decisions-from-wallet-behavior/) — Full MCP integration guide with code examples
- [Top 5 Ways Prediction MCP Will Turbocharge Your DeFi Platform](https://chainaware.ai/blog/top-5-ways-prediction-mcp-will-turbocharge-your-defi-platform/) — Lending, DEX, launchpad, governance, and personalization use cases
- [Why Personalization Is the Next Big Thing for AI Agents](https://chainaware.ai/blog/why-personalization-is-the-next-big-thing-for-ai-agents/) — The case for wallet-level personalization in Web3
- [DeFi Onboarding in 2026: Why 90% of Connected Wallets Never Transact and How AI Agents Fix It](https://chainaware.ai/blog/defi-onboarding-in-2026-why-90-of-connected-wallets-never-transact/) — Onboarding conversion problem and AI-driven solutions
- [The Web3 Agentic Economy: How AI Agents Are Replacing Human Teams in DeFi](https://chainaware.ai/blog/the-web3-agentic-economy-how-ai-agents-are-replacing-human-teams-in-defi/) — How autonomous AI agents are taking over DeFi operations and decision-making

### Agentic Commerce & KYA (Know Your Agent)
- [Know Your Agent (KYA): The Behavioral Trust Layer for AI Agents](https://chainaware.ai/blog/know-your-agent-kya-behavioral-trust-layer/) — Foundational KYA concept: why agent identity ≠ agent trust; behavioral trust layer for `agents_trust_score`
- [The First Step in Agentic Commerce Is Trust](https://chainaware.ai/blog/agentic-commerce-agent-trust-score/) — Why trust verification must precede integration; ERC-8004 as the agentic commerce trust primitive
- [The Agent Trust Infrastructure Race](https://chainaware.ai/blog/agent-trust-infrastructure-race-2026/) — Who is building the trust layer for AI agents; ChainAware's position in the emerging stack
- [ERC-8004 Trust Signals: What the Registry Can and Cannot Tell You](https://chainaware.ai/blog/erc-8004-trust-signals-what-a-registry-can-and-cannot-tell-you/) — What on-chain registration proves vs what behavioral scoring adds; `agents_trust_score` interpretation guide
- [Agent Fraud Patterns from the ERC-8004 Tracker](https://chainaware.ai/blog/agent-fraud-patterns-from-the-erc-8004-tracker/) — Live fraud pattern data from the weekly tracker; what `agents_trust_score` flags in practice
- [Agentic Commerce Fraud and the Four-Party Card Model](https://chainaware.ai/blog/agentic-commerce-fraud-four-party-card-model/) — Liability attribution when AI agents transact; fraud patterns in autonomous payment flows
- [Verifying AI Agents at Checkout](https://chainaware.ai/blog/verifying-ai-agents-at-checkout-tokens-mandates-behavior/) — Tokens, mandates, and behavioral evidence; `chainaware-agent-trust-screener` use case
- [Your Security Stack Was Built for Human Attackers](https://chainaware.ai/blog/ai-adversary-web2-web3-security/) — Why Web2/Web3 security tools fail against AI adversaries; behavioral trust as the new perimeter

### Tool Reference Docs (this repo)
- `references/tools-fraud.md` — `predictive_fraud` full schema, thresholds, forensic flags
- `references/tools-behaviour.md` — `predictive_behaviour` full schema, intent signals, personalization patterns
- `references/tools-batch.md` — `predictive_fraud_batch`, `predictive_behaviour_batch`, `check_job_status`, `get_job_results` — full batch pipeline, job lifecycle, Node.js examples
- `references/tools-rug-pull.md` — `predictive_rug_pull` full schema, 3-layer scoring model
- `references/tools-credit-score.md` — `credit_score` full schema, 1–9 rating scale, DeFi lending use cases
- `references/tools-token-rank-list.md` — `token_rank_list` full schema, categories, sorting, pagination
- `references/tools-token-rank-single.md` — `token_rank_single` full schema, top holders, global rank
- `references/tools-token-audit.md` — `run_token_audit` + `get_token_audit_result` — two-step deep audit pipeline, 8 modules, 0–100 risk score
- `references/tools-agent-trust-score.md` — `agents_trust_score_list` + `agents_trust_score_single` — ERC-8004 agent trust scores 0–1000

---

## Pending

- Publish skill to OpenClaw ClawHub (`clawhub publish`)
- Submit to OpenAI skill registry
