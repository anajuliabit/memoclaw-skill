# MemoClaw usage examples

## Example 1: CLI basics

The fastest way to use MemoClaw. Install once, then store and recall from any shell.

```bash
# Setup (one-time)
npm install -g memoclaw
memoclaw init    # Interactive wallet setup — saves to ~/.memoclaw/config.json
memoclaw status  # Check your free tier remaining

# Store a memory
memoclaw store "User prefers vim keybindings" --importance 0.8 --tags tools,preferences

# Recall memories
memoclaw recall "editor preferences" --limit 5

# Full-text keyword search (free, no embeddings)
memoclaw search "vim" --namespace default
```

## Example 2: OpenClaw agent session

Typical flow for an OpenClaw agent using MemoClaw throughout a session:

```bash
# Session start — load relevant context
memoclaw context "user preferences and recent decisions" --max-memories 10

# User says "I switched to Neovim last week"
memoclaw recall "editor preferences"         # check for existing memory
memoclaw store "User switched to Neovim (Feb 2026)" \
  --importance 0.85 --tags preferences,tools --memory-type preference

# User asks "what did we decide about the database?"
memoclaw recall "database decision" --namespace project-alpha

# Session end — store a summary
memoclaw store "Session 2026-02-16: Discussed editor migration, reviewed DB schema" \
  --importance 0.6 --tags session-summary

# Periodic maintenance
memoclaw consolidate --namespace default --dry-run
memoclaw suggested --category stale --limit 5
```

## Example 3: Ingesting raw text

Feed a conversation or document and let MemoClaw extract, deduplicate, and relate facts automatically.

```bash
# Extract facts from a sentence
memoclaw ingest "User's name is Ana. She lives in São Paulo. She works with TypeScript."

# Or from a file
memoclaw ingest "$(cat conversation.txt)" --namespace default --auto-relate

# Extract without dedup (just parse facts)
memoclaw extract "I prefer dark mode and use 2-space indentation"
```

## Example 4: Project namespaces

Keep memories organized per project.

```bash
# Store architecture decisions scoped to a project
memoclaw store "Team chose PostgreSQL over MongoDB for ACID requirements" \
  --importance 0.9 --tags architecture,database --namespace project-alpha

# Recall only from that project
memoclaw recall "what database did we choose?" --namespace project-alpha

# List all namespaces
memoclaw namespaces

# Export a project's memories
memoclaw export --format markdown --namespace project-alpha
```

## Example 5: Memory lifecycle

Storing, updating, pinning, relating, and deleting.

```bash
# Store
memoclaw store "User timezone is America/Sao_Paulo (UTC-3)" \
  --importance 0.7 --tags user-info --memory-type preference

# Update when things change
memoclaw update <uuid> --content "User timezone is America/New_York (UTC-5)" --importance 0.8

# Pin a memory so it never decays
memoclaw update <uuid> --pinned true

# Link related memories
memoclaw relations create <uuid-1> <uuid-2> supersedes

# View change history
memoclaw history <uuid>

# Delete when no longer relevant
memoclaw delete <uuid>
```

## Example 6: Consolidation and maintenance

Over time, memories fragment. Consolidate periodically.

```bash
# Preview what would merge (dry run)
memoclaw consolidate --namespace default --dry-run

# Actually merge duplicates
memoclaw consolidate --namespace default

# Find stale memories worth reviewing
memoclaw suggested --category stale --limit 10

# Bulk delete an old project
memoclaw purge --namespace old-project
```

## Example 7: Migrating from local markdown files

If you've been using `MEMORY.md` or `memory/*.md` files:

```bash
# Import all .md files at once
memoclaw migrate ./memory/

# Verify what was stored
memoclaw list --limit 50
memoclaw recall "user preferences"

# Pin the essentials
memoclaw update <id> --pinned true
```

Run both systems in parallel for a week before phasing out local files.

## Example 8: JavaScript SDK

```javascript
import { x402Fetch } from '@x402/fetch';

// Store a preference
await x402Fetch('POST', 'https://api.memoclaw.com/v1/store', {
  wallet: process.env.MEMOCLAW_PRIVATE_KEY,
  body: {
    content: "Ana prefers coffee without sugar, always in the morning",
    metadata: { tags: ["preferences", "food"] },
    importance: 0.8
  }
});

// Recall it later
const result = await x402Fetch('POST', 'https://api.memoclaw.com/v1/recall', {
  wallet: process.env.MEMOCLAW_PRIVATE_KEY,
  body: {
    query: "how does Ana like her coffee?",
    limit: 3
  }
});
```

## Example 9: Python SDK

```python
from memoclaw import MemoClawClient

async with MemoClawClient(private_key="0xYourKey") as client:
    result = await client.store(
        content="User prefers async/await over callbacks",
        importance=0.8,
        tags=["preferences", "code-style"],
        memory_type="preference",
    )

    memories = await client.recall(
        query="coding style preferences",
        limit=5,
        min_similarity=0.6,
    )
    for m in memories:
        print(f"[{m.similarity:.2f}] {m.content}")
```

Install: `pip install memoclaw`

## Cost breakdown

For a typical agent running daily:

| Activity | Operations | Cost |
|----------|-----------|------|
| 10 stores | 10 × $0.005 | $0.05 |
| 20 recalls | 20 × $0.005 | $0.10 |
| 2 list queries | Free | $0.00 |
| **Total** | | **~$0.15/day** |

Under $5/month for continuous agent memory. First 100 calls are free.
