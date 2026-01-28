# mcp-server-qdrant-repl

A fork of the official [Qdrant MCP server](https://github.com/qdrant/mcp-server-qdrant) that adds a `qdrant-repl` tool: a stateful Python REPL with a pre-configured `QdrantClient`.

## Why

The official MCP server gives agents two tools: store and find memories. That's useful for semantic memory, but agents working *with* Qdrant need full SDK access. This fork adds a REPL so agents can create collections, inspect schemas, run queries, and explore data directly.

Pairs well with a [skill.md](https://github.com/thierrypdamiba/skill-md) for static knowledge (gotchas, decision tables, correct API patterns).

## Tools

1. **`qdrant-store-memory`** - Store a memory in Qdrant (from upstream)
2. **`qdrant-find-memories`** - Retrieve memories by query (from upstream)
3. **`qdrant-repl`** - Execute Python with a pre-configured `QdrantClient`
   - Input: `code` (string)
   - Pre-configured globals: `client`, `models`, `json`
   - State persists between calls

## Usage

```shell
uv run mcp-server-qdrant \
  --qdrant-url "http://localhost:6333" \
  --qdrant-api-key "<your-api-key>" \
  --collection-name "my_collection"
```

### Claude Desktop

```json
{
  "qdrant": {
    "command": "uvx",
    "args": [
      "mcp-server-qdrant",
      "--qdrant-url", "http://localhost:6333",
      "--qdrant-api-key", "<your-api-key>",
      "--collection-name", "my_collection"
    ]
  }
}
```

### Claude Code

```json
{
  "mcpServers": {
    "qdrant": {
      "command": "uvx",
      "args": [
        "mcp-server-qdrant",
        "--qdrant-url", "http://localhost:6333",
        "--qdrant-api-key", "<your-api-key>",
        "--collection-name", "my_collection"
      ]
    }
  }
}
```

## REPL Examples

Once connected, agents can run:

```python
# List collections
[c.name for c in client.get_collections().collections]

# Inspect a collection
client.get_collection("products").config

# Search
client.query_points(
    "products",
    query=[0.1, 0.2, 0.3, 0.4],
    limit=5,
)

# Peek at data
client.scroll("products", limit=1)
```

Variables persist between calls, so agents build up context incrementally.

## Environment Variables

- `QDRANT_URL`: Qdrant server URL
- `QDRANT_API_KEY`: API key
- `COLLECTION_NAME`: Default collection name
- `EMBEDDING_MODEL`: Embedding model (default: `sentence-transformers/all-MiniLM-L6-v2`)
- `QDRANT_LOCAL_PATH`: Path for local Qdrant mode

## License

Apache License 2.0
