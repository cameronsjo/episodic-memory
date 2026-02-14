# Episodic Memory (Archived)

> **Status: Archived.** This fork is no longer actively used. See [Retirement Notes](#retirement-notes) for findings.

Semantic search for Claude Code conversations. Remember past discussions, decisions, and patterns across sessions.

> Maintained fork of [obra/episodic-memory](https://github.com/obra/episodic-memory) by [@cameronsjo](https://github.com/cameronsjo).

## Retirement Notes

Retired 2026-02-13 after extended evaluation. Replaced by [field-notes](https://github.com/cameronsjo/essentials) — curated insight capture at write-time rather than retrieval at search-time.

### What Worked

- Recency-based queries with date filters
- Tool-name searches (tool names appended to embeddings)
- Broad "what did I do last week" queries
- The `read` tool for pulling full conversation context once you find the right file

### What Didn't

| Issue | Root Cause |
|---|---|
| ~50% retrieval quality | `all-MiniLM-L6-v2` is a 384-dim BERT model from 2021 — good for general English, weak on technical/code content |
| Text search misses obvious variants | `LIKE '%query%'` substring matching — no stemming, no fuzzy. "implement" won't find "implementation" |
| Insights buried in long exchanges | Whole-exchange embedding (truncated to 2000 chars) averages the signal across the entire exchange |
| Multi-concept too restrictive | AND logic requires ALL concepts in same conversation — partial matches return nothing |
| Similar concepts conflated | Low-dimensional embeddings can't distinguish between e.g. different auth patterns or cache strategies |

### Architectural Lesson

The fundamental problem is trying to recover signal at search-time from raw conversation noise. Curating at write-time (field-notes, auto memory) is architecturally better — you distill when context is fresh, not when you're trying to remember.

### If You Want to Improve It

The highest-impact change would be swapping the embedding model to something modern that still runs locally (e.g. `nomic-embed-text` at 768-dim via Transformers.js). That's a one-file change in `src/embeddings.ts` + a full re-index. Adding FTS5 to SQLite would fix text search quality but doesn't address the core embedding problem.

---

## Original Documentation

<details>
<summary>Installation, usage, architecture (click to expand)</summary>

### Installation

#### As a Claude Code plugin

```bash
/plugin install episodic-memory@superpowers-marketplace
```

The plugin automatically:
- Indexes conversations at the end of each session
- Exposes MCP tools for searching and viewing conversations
- Makes your conversation history searchable via natural language

#### As an npm package

```bash
npm install episodic-memory
```

### Usage

```bash
# Sync conversations from Claude Code and index them
episodic-memory sync

# Search your conversation history
episodic-memory search "React Router authentication"

# View index statistics
episodic-memory stats

# Display a conversation
episodic-memory show path/to/conversation.jsonl
```

### Architecture

- **Core package** - TypeScript library for indexing and searching conversations
- **CLI tools** - Unified command-line interface for manual use
- **MCP Server** - Model Context Protocol server exposing search and conversation tools
- **Claude Code plugin** - Integration with Claude Code (auto-indexing, MCP tools, hooks)

### How It Works

1. **Sync** - Copies conversation files from `~/.claude/projects` to archive
2. **Parse** - Extracts user-agent exchanges from JSONL format
3. **Embed** - Generates vector embeddings using Transformers.js (local, offline)
4. **Index** - Stores in SQLite with sqlite-vec for fast similarity search
5. **Search** - Semantic search using vector similarity or exact text matching

</details>

## License

MIT

## Support

- **Upstream**: https://github.com/obra/episodic-memory
