# MemoClaw Usage Examples

All examples use x402 for payment. Your wallet address becomes your identity.

## Example 1: User Preferences

```javascript
import { x402Fetch } from '@x402/fetch';

// Store a preference
await x402Fetch('POST', 'https://api.memoclaw.dev/v1/store', {
  wallet: process.env.WALLET_KEY,
  body: {
    content: "Ana prefers coffee without sugar, always in the morning",
    metadata: { tags: ["preferences", "food"], context: "morning routine" },
    importance: 0.8
  }
});

// Recall it later
const result = await x402Fetch('POST', 'https://api.memoclaw.dev/v1/recall', {
  wallet: process.env.WALLET_KEY,
  body: {
    query: "how does Ana like her coffee?",
    limit: 3
  }
});

console.log(result.memories[0].content);
// → "Ana prefers coffee without sugar, always in the morning"
```

## Example 2: Project-Specific Knowledge

```javascript
// Store architecture decisions in a namespace
await x402Fetch('POST', 'https://api.memoclaw.dev/v1/store', {
  wallet: process.env.WALLET_KEY,
  body: {
    content: "Team decided PostgreSQL over MongoDB for ACID requirements",
    metadata: { tags: ["architecture", "database"] },
    importance: 0.9,
    namespace: "project-alpha"
  }
});

// Recall only from that project
const result = await x402Fetch('POST', 'https://api.memoclaw.dev/v1/recall', {
  wallet: process.env.WALLET_KEY,
  body: {
    query: "what database did we choose and why?",
    namespace: "project-alpha"
  }
});
```

## Example 3: Batch Import

```javascript
// Import multiple memories at once ($0.01 for up to 100)
await x402Fetch('POST', 'https://api.memoclaw.dev/v1/store/batch', {
  wallet: process.env.WALLET_KEY,
  body: {
    memories: [
      {
        content: "User timezone is America/Sao_Paulo (UTC-3)",
        metadata: { tags: ["user-info"] },
        importance: 0.7
      },
      {
        content: "User prefers responses in Portuguese for casual chat",
        metadata: { tags: ["preferences", "language"] },
        importance: 0.9
      },
      {
        content: "User works primarily with TypeScript and Bun runtime",
        metadata: { tags: ["tools", "tech-stack"] },
        importance: 0.8
      }
    ]
  }
});
```

## Example 4: Filtered Recall

```javascript
// Recall only recent food preferences
const result = await x402Fetch('POST', 'https://api.memoclaw.dev/v1/recall', {
  wallet: process.env.WALLET_KEY,
  body: {
    query: "food and drink preferences",
    limit: 10,
    min_similarity: 0.6,
    filters: {
      tags: ["food", "preferences"],
      after: "2025-01-01"
    }
  }
});
```

## Example 5: Memory Management

```javascript
// List all memories in a namespace
const list = await x402Fetch('GET', 
  'https://api.memoclaw.dev/v1/memories?namespace=project-alpha&limit=50',
  { wallet: process.env.WALLET_KEY }
);

console.log(`Found ${list.total} memories`);

// Delete a specific memory
await x402Fetch('DELETE', 
  `https://api.memoclaw.dev/v1/memories/${memoryId}`,
  { wallet: process.env.WALLET_KEY }
);
```

## Example 6: CLI Usage

Using the x402 CLI directly:

```bash
# Store a memory
npx @x402/cli pay POST https://api.memoclaw.dev/v1/store \
  --wallet ~/.wallet/key \
  --data '{"content": "User prefers vim keybindings", "importance": 0.8}'

# Recall memories
npx @x402/cli pay POST https://api.memoclaw.dev/v1/recall \
  --wallet ~/.wallet/key \
  --data '{"query": "editor preferences"}'
```

## Example 7: Agent Memory Layer

Integrate MemoClaw as a persistent memory layer for any AI agent:

```javascript
class AgentMemory {
  constructor(walletKey) {
    this.wallet = walletKey;
    this.namespace = 'agent-main';
  }

  async remember(content, importance = 0.5, tags = []) {
    // Check for similar existing memory first
    const existing = await this.recall(content, { limit: 1, min_similarity: 0.9 });
    if (existing.memories.length > 0) {
      console.log('Similar memory exists, skipping');
      return existing.memories[0];
    }
    
    return x402Fetch('POST', 'https://api.memoclaw.dev/v1/store', {
      wallet: this.wallet,
      body: { content, importance, metadata: { tags }, namespace: this.namespace }
    });
  }

  async recall(query, options = {}) {
    return x402Fetch('POST', 'https://api.memoclaw.dev/v1/recall', {
      wallet: this.wallet,
      body: { query, namespace: this.namespace, ...options }
    });
  }

  async onUserCorrection(wrongAssumption, correction) {
    // Store corrections with high importance
    await this.remember(
      `Correction: Previously assumed "${wrongAssumption}" but actually: ${correction}`,
      0.95,
      ['correction', 'learning']
    );
  }

  async onSessionStart() {
    // Load recent high-importance memories
    return this.recall('recent important context', {
      limit: 10,
      min_similarity: 0.3 // Lower threshold for broad context
    });
  }
}
```

## Cost Breakdown

For typical agent usage:

| Daily Activity | Operations | Cost |
|----------------|------------|------|
| 10 stores | 10 × $0.001 | $0.01 |
| 20 recalls | 20 × $0.001 | $0.02 |
| 2 list queries | 2 × $0.0005 | $0.001 |
| **Total** | | **~$0.03/day** |

At ~$0.03/day, that's less than $1/month for continuous agent memory.
