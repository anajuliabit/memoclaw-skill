# MemoClaw — Competitive Landscape (Feb 2026)

## Quick Comparison

| Feature | **MemoClaw** | **Mem0** | **Zep** |
|---------|-------------|----------|---------|
| **Auth model** | Wallet-based (no signup) | API key + account | API key + account |
| **Payment** | x402 micropayments / free tier | Subscription plans | Subscription plans |
| **Free tier** | 1000 calls/wallet | Limited free plan | Limited free plan |
| **Identity** | Wallet address | user_id string | user_id string |
| **Search** | Semantic vector search | Semantic + graph | Temporal knowledge graph |
| **Memory decay** | Type-based half-lives | No native decay | Temporal invalidation |
| **Relations** | Explicit (supersedes, contradicts, etc.) | Auto-extracted | Auto graph edges |
| **Consolidation** | Rule-based + LLM merge | Auto-dedup | Auto via graph |
| **Self-hosted** | No (SaaS only) | Yes (OSS option) | Yes (OSS option) |
| **SDK languages** | JS/TS (CLI + x402Fetch) | Python, JS, REST | Python, JS, REST |
| **Graph memory** | No (flat + relations) | Yes (knowledge graph) | Yes (temporal graph) |
| **MCP support** | Not yet | Yes | Yes |
| **SOC 2 / GDPR** | Not advertised | SOC 2 Type II | Enterprise tier |
| **Multimodal** | Text only | Text + images | Text only |
| **Pricing model** | Pay-per-call ($0.001) | Seat/usage plans | Seat/usage plans |

## MemoClaw Differentiators

1. **Zero-signup onboarding** — wallet address = identity. No accounts, no API keys, no email verification. Unique in the market.
2. **Micropayment model** — pay exactly for what you use via x402. No monthly minimums.
3. **Generous free tier** — 1000 calls free per wallet, no credit card.
4. **Memory decay system** — type-based half-lives with pinning. Neither Mem0 nor Zep has this natively.
5. **Crypto-native** — built for the on-chain agent ecosystem (x402, USDC on Base).

## Where Competitors Are Ahead

1. **Graph memory** — Both Mem0 and Zep offer knowledge graphs that auto-extract entities and relationships. MemoClaw's flat-with-relations model is simpler but less powerful for complex reasoning.
2. **Self-hosted option** — Both competitors offer open-source self-hosted versions. MemoClaw is SaaS-only.
3. **MCP integration** — Mem0 has an MCP server, making it plug-and-play with Claude Desktop and similar tools.
4. **Enterprise compliance** — Mem0 has SOC 2 Type II. Important for enterprise adoption.
5. **Multi-language SDKs** — Mem0 has Python + JS SDKs. MemoClaw is JS/CLI only.
6. **Multimodal** — Mem0 supports image memories.

## Opportunities

- **MCP server** — Adding an MCP server would make MemoClaw compatible with Claude Desktop, Cursor, and other MCP clients. Low effort, high impact.
- **Python SDK** — Many AI agents are Python-based. A Python client would expand reach significantly.
- **Graph layer** — Even a simple entity extraction layer on top of existing relations would close the gap with Mem0/Zep.
- **Self-hosted tier** — Even a Docker-based single-node option would appeal to privacy-conscious users.

## Target Audience Fit

| Audience | Best fit |
|----------|----------|
| Crypto/web3 agents | **MemoClaw** (wallet-native, x402) |
| Enterprise chatbots | Mem0 or Zep (compliance, scale) |
| Hobbyist/indie devs | **MemoClaw** (free tier, no signup) |
| Python ML pipelines | Mem0 (Python SDK, OSS) |
| Complex multi-entity reasoning | Zep (temporal graph) |
