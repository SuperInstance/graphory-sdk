# Graphory Python SDK

Query your knowledge graph from Claude Code, Cursor, or any Python script.

## Install

```bash
pip install graphory
```

Or install from source:

```bash
pip install -e ./sdk
```

## Quick Start

```python
from graphory import Graphory

g = Graphory(api_key="gs_ak_...", org_id="org_01...")

# Search the graph
results = g.search("Jacob Kriser")
for r in results:
    print(f"{r['label']}: {r['name']}")

# Get a specific entity with its edges
entity = g.entity("contact:jacob@example.com")
print(entity)

# Multi-hop traversal
paths = g.traverse("contact:jacob@example.com", depth=2, edge_types=["works_for", "sent"])
for p in paths["paths"]:
    print(f"{p['from_name']} --{p['edge_type']}--> {p['to_name']}")

# Timeline of recent activity
events = g.timeline(entity="groundstone-group", days=30)
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
