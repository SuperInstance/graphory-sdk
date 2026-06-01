# Graphory Python SDK

Python SDK + CLI for Graphory -- durable cognition for businesses and the AI that serves them.

Query and write to your knowledge graph from Claude Code, Cursor, or any Python script.

**Status:** pre-release / alpha (0.1.0). API is stable for the documented endpoints.

## Install

```bash
pip install graphory
```

Install from source:

```bash
git clone https://github.com/groundstone-group/graphory-sdk.git
cd graphory-sdk
pip install -e .
```

## Two-step onboarding

1. Sign up at https://graphory.io and create an API key.
2. Run `graphory login` to save credentials to `~/.graphory/config.json` (chmod 600).

```bash
graphory login         # prompts for API key + org_id, validates against /stats
graphory status        # shows current login + live node/edge counts
graphory logout        # deletes the local config
graphory --version
```

Once logged in, Python scripts can pick up credentials with no args:

```python
from graphory import Graphory

g = Graphory.from_config()      # reads ~/.graphory/config.json
print(g.stats())
```

## Quick Start

```python
from graphory import Graphory

g = Graphory(api_key="gs_ak_...", org_id="org_01...")

# Search the graph
results = g.search("Alex Rivera")
for r in results:
    print(f"{r['label']}: {r['name']}")

# Get a specific entity with its edges
entity = g.entity("contact:alex.rivera@example.com")
print(entity)

# Multi-hop traversal
paths = g.traverse("contact:alex.rivera@example.com", depth=2, edge_types=["works_for", "sent"])
for p in paths["paths"]:
    print(f"{p['from_name']} --{p['edge_type']}--> {p['to_name']}")

# Timeline of recent activity
events = g.timeline(entity="acme-advisors", days=30)
for e in events:
    print(f"{e['occurred_at']}: {e['name']}")

# Graph statistics
stats = g.stats()
print(f"Nodes: {stats['total_nodes']}, Edges: {stats['total_edges']}")

# Write to the graph
g.write(
    action="upsert_node",
    label="Person",
    node_id="contact:new@example.com",
    properties={"name": "New Contact", "email": "new@example.com"},
    confidence=0.95,
)

# Save a conversation (2-step)
schema = g.conversation_schema()
print(schema)  # shows the expected YAML frontmatter format

g.save_conversation(data="""---
title: Sales call with Acme Corp
type: meeting
company: acme-corp
domain: operations
who:
  - met with John Smith
  - discussed pricing
date_published: 2026-04-06
---
Discussed Q2 pricing. John wants a 10% discount on the annual plan.
""")
```

## Environment Variables

You can load credentials from environment variables:

```python
import os
from graphory import Graphory

g = Graphory(
    api_key=os.environ["GRAPHORY_API_KEY"],
    org_id=os.environ["GRAPHORY_ORG_ID"],
)
```

## API Reference

### `Graphory(api_key, org_id, base_url="https://api.graphory.io", timeout=30)`

Create a client instance.

### `Graphory.from_config(config_path=None, timeout=30)`

Load credentials from `~/.graphory/config.json` (written by `graphory login`).

### `g.search(query, limit=20, node_type=None, entity=None)`

Search the graph. Returns list of matching node dicts.

### `g.entity(entity_id)`

Get a node and its 1-hop neighborhood.

### `g.traverse(start_id, depth=2, edge_types=None)`

Multi-hop graph traversal. Returns paths with from/to node info and edge types.

### `g.timeline(entity=None, days=30, limit=50)`

Recent activities sorted by date.

### `g.stats()`

Node and edge counts by type.

### `g.connections()`

List active data source connections.

### `g.write(action, label=None, node_id=None, properties=None, from_id=None, to_id=None, edge_type=None, confidence=0.95, evidence=None)`

Write nodes or edges with confidence gating.

### `g.conversation_schema()`

Get the YAML frontmatter format for saving conversations (Step 1).

### `g.save_conversation(data)`

Save structured conversation data as .md with YAML frontmatter (Step 2).

### `g.usage()`

Get current node count, plan limits, and usage percentage.

### `g.close()`

Close the HTTP client. Also works as a context manager:

```python
with Graphory(api_key="...", org_id="...") as g:
    results = g.search("test")
```

## Error Handling

```python
from graphory import Graphory, GraphoryError

g = Graphory(api_key="gs_ak_...", org_id="org_01...")

try:
    results = g.search("test")
except GraphoryError as e:
    print(f"Status {e.status_code}: {e.message}")
```

## Links

- Docs: https://docs.graphory.io
- Marketing site: https://graphory.io
- Issues: https://github.com/groundstone-group/graphory-sdk/issues
- Changelog: https://github.com/groundstone-group/graphory-sdk/blob/main/CHANGELOG.md

## License

MIT. See [LICENSE](LICENSE).
