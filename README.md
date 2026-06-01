# Graphory

**Graphory is the business memory layer for AI agents.**

Connect your real operational data (email, calls, CRM, invoices) to a per-org knowledge graph that any AI client can query, write to, and reason over across sessions.

```bash
pip install graphory
graphory login
```

```python
from graphory import Graphory
g = Graphory.from_config()
results = g.search("emails from Derek about the closing")
```

![PyPI](https://img.shields.io/pypi/v/graphory)
![Python](https://img.shields.io/pypi/pyversions/graphory)
![License](https://img.shields.io/pypi/l/graphory)
![Downloads](https://img.shields.io/pypi/dm/graphory)

## What is Graphory?

Graphory is real business memory for AI agents. It ingests your operational data (Gmail, QuickBooks, Slack, CRMs, calls, invoices, files) into a typed, per-org knowledge graph. Any AI client can connect via MCP or HTTP, query across sources, and write findings back. Memory survives across sessions, models, and tools. One graph per org, isolated and yours.

The SDK is open source and thin. The Graphory service behind it does the heavy lifting: a universal deterministic extractor, a master ontology accumulated across users, identity resolution, and temporal provenance on every node and edge. No LLM sits in the extraction pipeline, so the same input produces the same graph on every run at zero inference cost. Credentials stay yours (BYOC) and are stored encrypted per-org in WorkOS Vault.

## Built for

- **Hermes users** who hit the agent-memory churn problem on every long-running task
- **OpenClaw users** who need durable operational data, not just chat transcripts
- **Claude Code and Cursor developers** wiring AI into business systems over MCP
- **Anyone building production AI workflows** that need real, queryable memory of the business

Graphory works with Hermes, OpenClaw, Claude Code, Cursor, ChatGPT, and any MCP-capable client. One `gs_ak_` key, one MCP endpoint, every agent sees the same graph.

## Quick start

```bash
pip install graphory
graphory login   # one-time auth, stores config at ~/.graphory/config.json
```

```python
from graphory import Graphory

g = Graphory.from_config()

# Search across the graph
results = g.search("emails from Derek about the closing")

# Multi-hop traversal
paths = g.traverse("contact:derek@example.com", depth=2)

# Timeline for an entity
events = g.timeline(entity="acme-advisors", days=30)

# Write a finding back to the graph
g.write(
    action="upsert_node",
    label="Person",
    node_id="contact:new@example.com",
    properties={"name": "New Contact"},
    confidence=0.95,
)
```

## What you can build

- **"When did I last talk to Derek?"** Single-hop traversal across calls, texts, emails, and meetings in one call. Returns the most recent activity with full source provenance.
- **"What invoices haven't been paid 30+ days?"** Cross-source query over QuickBooks invoices and Gmail follow-ups. The graph already joins them on the customer entity.
- **"Send a follow-up text to every customer who churned this quarter."** Read with Graphory, then act through your messaging tool of choice. The graph gives the list; your agent does the work.

## Architecture

```
Your real data (Gmail, QuickBooks, Slack, CRM, calls)
         |
         v
BYOC credentials in your WorkOS Vault (encrypted, per-org)
         |
         v
Universal extractor (deterministic, no LLM in the pipeline)
         |
         v
Per-org FalkorDB graph (typed nodes, edges, temporal provenance)
         |
         v
Your AI client (Hermes, OpenClaw, Claude Code, Cursor, ChatGPT)
   via MCP at api.graphory.io/mcp or HTTP at api.graphory.io
```

Seven generic node types (Business, Person, Organization, Activity, Asset, Account, Thread) cover any domain. Industry-specific facets live as properties, not as new types. Every node and edge carries source, confidence, authority, and timestamp.

## API surface

**HTTP endpoints** (api.graphory.io):
- `POST /search` keyword search across the org graph
- `GET /entity/{id}` full entity with 1-hop neighborhood
- `POST /traverse` multi-hop traversal
- `GET /timeline/{entity}` temporal activity feed
- `POST /write` confidence-gated node and edge writes
- `GET /stats` node and edge counts by type
- `POST /ingest` universal webhook ingestion

**MCP tools** (api.graphory.io/mcp): 49 total. 24 read tools, 21 write tools, 4 review tools. See https://docs.graphory.io/mcp for the full list.

**Authentication:** `gs_ak_` API keys, one per org. Use the same key from HTTP, MCP, and the Python SDK.

Full reference: https://docs.graphory.io/api

## How it compares

**Is Graphory like Mem0?**
Not really. Mem0 stores chat facts in vectors and key-value pairs. Graphory stores business entities (people, companies, invoices, threads, deals) in a typed graph with temporal provenance. Different shape, different use case.

**Is Graphory like Zep?**
Zep is an enterprise chat-context lake. Graphory is for operational data (real emails, real invoices, real calls) joined into one graph per org. You can push chat into Graphory too, but the primary input is the business itself.

**Why no LLM in the extraction pipeline?**
Deterministic extraction is replay-safe, auditable, and free. The same `.md` file produces the same graph on every run. AI is bounded to a soft advisor layer where corrections feed extraction rules over time. The core is hard logic, not vibes.

**How does this work with Claude Max?**
The advisor layer that proposes new rules runs as a `claude -p` subprocess against your Claude subscription. No Anthropic API tokens are required on the Graphory side. The user-facing MCP server is plain HTTP with a `gs_ak_` key.

## Links

- Homepage: https://graphory.io
- Docs: https://docs.graphory.io
- MCP guide: https://docs.graphory.io/mcp
- Benchmarks: https://docs.graphory.io/benchmarks
- Issues: https://github.com/groundstone-group/graphory-sdk/issues
- PyPI: https://pypi.org/project/graphory/

## License and status

MIT licensed. Beta status (semver from 0.1.0). API surface is stable; minor changes during the beta will be documented in `CHANGELOG.md`. Open an issue if something breaks.
