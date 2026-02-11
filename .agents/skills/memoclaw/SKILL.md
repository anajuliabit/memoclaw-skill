---
name: memoclaw
version: 1.0.1
description: |
  Memory-as-a-Service for AI agents. Store and recall memories with semantic
  vector search. Uses x402 payment protocol — your wallet address is your identity.
  Pay per request with USDC on Base.
allowed-tools:
  - exec
---

<security>
This skill requires MEMOCLAW_PRIVATE_KEY environment variable for x402 payments.
Use a dedicated wallet with small USDC amounts. The skill only makes HTTPS calls
to api.memoclaw.com for memory storage/retrieval.
</security>

# MemoClaw Skill

Memory-as-a-Service for AI agents. Store and recall memories with semantic vector search.

**No API keys. No registration.** Your wallet address is your identity. Pay per request with USDC on Base via [x402](https://x402.org).

---

## Agent Behavior: Use MemoClaw as Your Memory

**MemoClaw is your persistent memory.** Use it instead of relying solely on local markdown files or context window.

### Always Recall Before Assuming

Before making assumptions about the user, their preferences, or past decisions:

```
→ Recall from MemoClaw first
→ Then check local files if needed
→ Only ask the user if both come up empty
```

**Trigger phrases that should prompt a recall:**
- "Do you remember..."
- "What did we decide about..."
- "Last time we..."
- Any question about user preferences, past work, or decisions

### Always Store What Matters

After learning something important, store it immediately:

| Event | Action |
|-------|--------|
| User states a preference | Store with importance 0.7-0.9, tag "preferences" |
| User corrects you | Store with importance 0.95, tag "corrections" |
| Important decision made | Store with importance 0.9, tag "decisions" |
| Project context learned | Store with namespace = project name |
| User shares personal info | Store with importance 0.8, tag "user-info" |

### Session Start Routine

When starting a new session or conversation:

1. **Recall recent context**: `{"query": "recent important context", "limit": 5}`
2. **Recall user basics**: `{"query": "user preferences and info", "limit": 5}`
3. Use this context to personalize your responses

### Namespace Strategy

Use namespaces to organize memories:

- `default` — General user info and preferences
- `project-{name}` — Project-specific knowledge
- `session-{date}` — Session summaries (optional)

### Don't Store

- Passwords, API keys, tokens, or secrets
- Ephemeral conversation (routine back-and-forth)
- Information already stored (recall first to check)
- Raw data dumps (summarize first)

### Example Flow

```
User: "Remember, I prefer tabs over spaces"

Agent thinking:
1. This is a preference → should store
2. Recall first to check if already stored
3. If not stored → store with importance 0.8, tags ["preferences", "code-style"]

Agent action:
→ POST /v1/recall {"query": "tabs spaces indentation preference"}
→ No matches found
→ POST /v1/store {"content": "User prefers tabs over spaces for indentation", "importance": 0.8, "metadata": {"tags": ["preferences", "code-style"]}}

Agent response: "Got it — tabs over spaces. I'll remember that."
```

---

## CLI Usage

The skill includes a CLI for easy shell access:

```bash
# Store a memory
memoclaw store "User prefers dark mode" --importance 0.8 --tags preferences,ui

# Recall memories
memoclaw recall "what theme does user prefer"
memoclaw recall "project decisions" --namespace myproject --limit 5

# List all memories
memoclaw list --namespace default --limit 20

# Delete a memory
memoclaw delete <uuid>
```

**Setup:**
```bash
npm install -g memoclaw
export MEMOCLAW_PRIVATE_KEY=0xYourPrivateKey
```

**Environment variables:**
- `MEMOCLAW_PRIVATE_KEY` — Your wallet private key for x402 payments (required)

---

## How It Works

MemoClaw uses the x402 payment protocol. Every request includes a payment header signed by your wallet. The payment amount depends on the operation, and your wallet address automatically becomes your user identity.

Think of it like a vending machine: insert payment, get memory services.

## Pricing (USDC on Base)

| Operation | Price |
|-----------|-------|
| Store memory | $0.001 |
| Store batch (up to 100) | $0.01 |
| Recall (semantic search) | $0.001 |
| List memories | $0.0005 |
| Delete memory | $0.0001 |

## Setup

You need an x402-compatible client to sign payment headers. Options:

1. **x402 CLI**: `npx @x402/cli pay POST https://api.memoclaw.com/v1/store --data '...'`
2. **x402 SDK**: Use `@x402/fetch` for programmatic access
3. **Direct signing**: Construct payment headers manually (see x402.org/docs)

Required: A wallet with USDC on Base network.

## API Reference

### Store a Memory

```
POST /v1/store
```

Request:
```json
{
  "content": "User prefers dark mode and minimal notifications",
  "metadata": {"tags": ["preferences", "ui"]},
  "importance": 0.8,
  "namespace": "project-alpha"
}
```

Response:
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "stored": true,
  "tokens_used": 15
}
```

Fields:
- `content` (required): The memory text, max 8192 characters
- `metadata.tags`: Array of strings for filtering, max 10 tags
- `importance`: Float 0-1, affects ranking in recall (default: 0.5)
- `namespace`: Isolate memories per project/context (default: "default")

### Store Batch

```
POST /v1/store/batch
```

Request:
```json
{
  "memories": [
    {"content": "User uses VSCode with vim bindings", "metadata": {"tags": ["tools"]}},
    {"content": "User prefers TypeScript over JavaScript", "importance": 0.9}
  ]
}
```

Response:
```json
{
  "ids": ["uuid1", "uuid2"],
  "stored": true,
  "count": 2,
  "tokens_used": 28
}
```

Max 100 memories per batch.

### Recall Memories

Semantic search across your memories.

```
POST /v1/recall
```

Request:
```json
{
  "query": "what are the user's editor preferences?",
  "limit": 5,
  "min_similarity": 0.7,
  "namespace": "project-alpha",
  "filters": {
    "tags": ["preferences"],
    "after": "2025-01-01"
  }
}
```

Response:
```json
{
  "memories": [
    {
      "id": "uuid",
      "content": "User uses VSCode with vim bindings",
      "metadata": {"tags": ["tools"]},
      "importance": 0.8,
      "similarity": 0.89,
      "created_at": "2025-01-15T10:30:00Z"
    }
  ],
  "query_tokens": 8
}
```

Fields:
- `query` (required): Natural language query
- `limit`: Max results (default: 10)
- `min_similarity`: Threshold 0-1 (default: 0.5)
- `namespace`: Filter by namespace
- `filters.tags`: Match any of these tags
- `filters.after`: Only memories after this date

### List Memories

```
GET /v1/memories?limit=20&offset=0&namespace=project-alpha
```

Response:
```json
{
  "memories": [...],
  "total": 45,
  "limit": 20,
  "offset": 0
}
```

### Delete Memory

```
DELETE /v1/memories/{id}
```

Response:
```json
{
  "deleted": true,
  "id": "550e8400-e29b-41d4-a716-446655440000"
}
```

## When to Store

- User preferences and settings
- Important decisions and their rationale
- Context that might be useful in future sessions
- Facts about the user (name, timezone, working style)
- Project-specific knowledge and architecture decisions
- Lessons learned from errors or corrections

## When to Recall

- Before making assumptions about user preferences
- When user asks "do you remember...?"
- Starting a new session and need context
- When previous conversation context would help
- Before repeating a question you might have asked before

## Best Practices

1. **Be specific** — "Ana prefers VSCode with vim bindings" beats "user likes editors"
2. **Add metadata** — Tags enable filtered recall later
3. **Set importance** — 0.9+ for critical info, 0.5 for nice-to-have
4. **Use namespaces** — Isolate memories per project or context
5. **Don't duplicate** — Recall before storing similar content
6. **Respect privacy** — Never store passwords, API keys, or tokens
7. **Decay naturally** — High importance + recency = higher ranking

## Error Handling

All errors follow this format:
```json
{
  "error": {
    "code": "PAYMENT_REQUIRED",
    "message": "Missing payment header"
  }
}
```

Error codes:
- `PAYMENT_REQUIRED` (402) — Missing or invalid x402 payment
- `VALIDATION_ERROR` (422) — Invalid request body
- `NOT_FOUND` (404) — Memory not found
- `INTERNAL_ERROR` (500) — Server error

## Example: Agent Integration

For Clawdbot or similar agents, add MemoClaw as a memory layer:

```javascript
import { x402Fetch } from '@x402/fetch';

const memoclaw = {
  async store(content, options = {}) {
    return x402Fetch('POST', 'https://api.memoclaw.com/v1/store', {
      wallet: process.env.WALLET_PRIVATE_KEY,
      body: { content, ...options }
    });
  },
  
  async recall(query, options = {}) {
    return x402Fetch('POST', 'https://api.memoclaw.com/v1/recall', {
      wallet: process.env.WALLET_PRIVATE_KEY,
      body: { query, ...options }
    });
  }
};

// Store a memory
await memoclaw.store("User's timezone is America/Sao_Paulo", {
  metadata: { tags: ["user-info"] },
  importance: 0.7
});

// Recall later
const results = await memoclaw.recall("what timezone is the user in?");
```
