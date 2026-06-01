# Graphory

**Stop your AI from forgetting your business.**

Graphory is the cross-AI business memory layer. Your emails, calls, invoices, CRM, and chat sessions land in one typed knowledge graph that any AI client can query, write to, and reason over across sessions, tools, and models.

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

## Built for

- **Hermes users** who hit the agent-memory churn problem on every long-running task
- **OpenClaw users** who need durable operational data, not just chat transcripts
- **Claude Code and Cursor developers** wiring AI into business systems over MCP
- **ChatGPT power users** tired of starting from scratch in every new thread
- **Anyone building production AI workflows** over real business data

One API key. One MCP endpoint. Every agent sees the same graph.

## What is Graphory?

Graphory is real business memory for AI agents. It ingests your operational data (Gmail, QuickBooks, Slack, CRM, calls, invoices, files) into a typed knowledge graph, then lets any AI client read and write that graph through HTTP or MCP.

The differentiators:

- **Operational data, not just chat.** Real emails, real invoices, real call logs, real CRM records, joined on real entities (people, companies, deals, threads).
- **Cross-AI memory.** Every AI client can save chat sessions to your graph and query previous sessions saved by other clients. Claude Desktop saves a strategy thread, Cursor reads it the next day, Hermes references it the week after. One graph, every AI, every session.
- **Per-organization isolation.** Your data lives in its own graph. Nothing crosses orgs, ever.
- **Encrypted credential vault.** No plaintext on disk. Tokens stay yours (bring your own credentials).
- **Deterministic extraction.** No LLM in the ingestion pipeline. Same input produces the same graph on every run. Zero inference cost, zero drift, zero hallucinated invoices.
- **Temporal provenance.** Every node and edge carries source, confidence, authority, and timestamp. Full audit trail.

The SDK is open source and thin. The Graphory service behind it does the heavy lifting: a universal extractor, a master ontology that accumulates across users, identity resolution, and the cross-AI session layer.

## What you can build

- **"When did I last hear from Derek?"** One hop across calls, texts, emails, and meetings. Returns the actual thread with full source provenance.
- **"Which invoices have been outstanding 30+ days?"** Cross-source AR query over accounting and inbox follow-ups, joined on the customer entity automatically.
- **"What did my last AI session decide about the Smith deal?"** Pulls the prior chat session (saved by whatever AI client you used), including decisions, action items, and the linked threads it referenced.

## Architecture

```
Your real data (Gmail, QuickBooks, Slack, CRM, phone, calendar, files)
         |
         v
Encrypted credential vault (per-org isolation, no plaintext on disk)
         |
         v
Universal extractor (deterministic, zero LLM, replay-safe, $0 inference)
         |
         v
Per-org isolated knowledge graph (typed nodes, temporal provenance)
         |
         v
Your AI client (Hermes, OpenClaw, Claude Code, Cursor, ChatGPT, custom)
   via MCP at api.graphory.io/mcp or HTTPS at api.graphory.io
```

Seven generic node types (Business, Person, Organization, Activity, Asset, Account, Thread) cover any domain. Industry-specific facets live as properties, not as new types.

## Free tier

Start free. 100,000 nodes, no credit card, full read and write access. Connect your tools, query from your AI, see what business memory feels like before you pay a cent. Pro and Business tiers when you outgrow it. Pricing: [graphory.io/pricing](https://graphory.io/pricing).

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

## API surface

**HTTP endpoints** (api.graphory.io):
- `POST /search` keyword search across the org graph
- `GET /entity/{id}` full entity with 1-hop neighborhood
- `POST /traverse` multi-hop traversal
- `GET /timeline/{entity}` temporal activity feed
- `POST /write` confidence-gated node and edge writes
- `GET /stats` node and edge counts by type
- `POST /ingest` universal webhook ingestion

**MCP tools** (api.graphory.io/mcp): read, write, and review tools for any AI client. See [docs.graphory.io/mcp](https://docs.graphory.io/mcp) for the full list.

**Authentication:** `gs_ak_` API keys, one per org. The same key works from HTTP, MCP, and the Python SDK.

Full reference: [docs.graphory.io/api](https://docs.graphory.io/api).

## How it compares

**Is Graphory like Mem0?**
Mem0 remembers chat. Graphory remembers your business: emails, invoices, calls, CRM activity, plus chat sessions. When you ask "when did I last hear from Derek about pricing," the answer is the actual email thread, not a paraphrased note.

**Is Graphory like Zep?**
Zep is an enterprise chat-context lake. Graphory is operational memory: real business activity tied to real people, real companies, real deals. Different input, different output.

**Is Graphory like Cognee?**
Cognee builds graphs from conversation with LLMs writing into the graph as they go, which drifts over time. Graphory's core extraction is deterministic: same input, same graph, every run. The AI advisor layer is bounded to suggestions you approve, not core writes.

**Why no LLM in the extraction pipeline?**
Three reasons: zero inference cost, replay-safe (same input equals same output, always), and you never get a hallucinated invoice. Predictability is the feature.

**How does memory work across my different AI clients?**
Every AI client can save chat sessions to your graph and query previous sessions saved by other clients. Claude Desktop saves a strategy conversation, Cursor reads it later, Hermes references it tomorrow. One graph, every AI, every session. This is the killer feature no chat-bound memory product can match.

**How does this work with Claude Max?**
The advisor layer runs as a `claude -p` subprocess against your Claude subscription. No Anthropic API tokens required on the Graphory side. The user-facing MCP server is plain HTTPS with a `gs_ak_` key.

## Links

- Homepage: [graphory.io](https://graphory.io)
- Pricing: [graphory.io/pricing](https://graphory.io/pricing)
- Docs: [docs.graphory.io](https://docs.graphory.io)
- MCP guide: [docs.graphory.io/mcp](https://docs.graphory.io/mcp)
- Benchmarks: [docs.graphory.io/benchmarks](https://docs.graphory.io/benchmarks)
- Issues: [github.com/groundstone-group/graphory-sdk/issues](https://github.com/groundstone-group/graphory-sdk/issues)
- PyPI: [pypi.org/project/graphory](https://pypi.org/project/graphory/)

## License and status

MIT licensed. Beta status (semver from 0.1.0). API surface is stable; minor changes during the beta are documented in `CHANGELOG.md`. Open an issue if something breaks.
