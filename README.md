# MemoClaw Skill

Semantic memory API for AI agents. Wallet = identity.

## Install

```bash
npx skills add anajuliabit/memoclaw-skill
```

Or manually copy `SKILL.md` to your agent's skills directory.

## Usage

```bash
# Set your private key
export MEMOCLAW_PRIVATE_KEY=0x...

# Store a memory
memoclaw store "Meeting notes: discussed Q1 roadmap" --importance 0.8 --tags work

# Recall memories
memoclaw recall "what did we discuss about roadmap"
```

## Pricing

**Free Tier:** 1000 calls per wallet — no payment required.

After free tier (USDC on Base):
- Store: $0.001
- Recall: $0.001
- List: $0.0005
- Delete: $0.0001

Your wallet address is your identity — no signup needed.

## Links

- **API**: https://api.memoclaw.com
- **Docs**: https://memoclaw.com/docs
- **Website**: https://memoclaw.com

## License

MIT
