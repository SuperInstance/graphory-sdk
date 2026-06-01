# Changelog

All notable changes to the Graphory Python SDK are documented in this file.

## [Unreleased] - 2026-06-01 (third pass)

### Changed
- README FAQ trimmed: removed an entry that exposed advisor-layer implementation details. Cost framing in the remaining FAQ is benefit-led ("zero inference cost", "predictable pricing"), not architecture.

## [Unreleased] - 2026-06-01

### Changed
- README rewritten (second pass) with bolder positioning ("Stop your AI from forgetting your business"), free-tier callout, and the cross-AI chat session memory differentiator promoted to a first-class feature.
- All customer-facing vendor names stripped from README, llms.txt, and pyproject description. Replaced with benefit language ("encrypted credential vault", "per-org isolated knowledge graph") so the public surface no longer reveals which auth provider or graph database backs the service.
- `pyproject.toml` description rewritten to lead with the value prop, not the implementation.
- llms.txt updated with the cross-AI session memory angle, free-tier mention, and stripped vendor names.

### Added
- Cross-AI chat session memory framing in README ("save_conversation across clients", "one graph, every AI, every session") backed by the shipped `save_conversation` MCP tool and `POST /org/{id}/conversation` API endpoint.
- Free-tier section in the README (100K nodes, no credit card).
- New keywords: `cursor`, `chatgpt`, `zep-alternative`, `cross-ai-memory`, `chat-session-memory`.

## [Unreleased] - 2026-06-01 (first pass)

### Changed
- README rewritten with full positioning (hero, what-is, built-for, architecture, FAQ, links).
- `pyproject.toml` description and keywords expanded for SEO and AEO discoverability.
- Development status moved from Alpha to Beta.

### Added
- `llms.txt` at repository root for LLM and AEO crawlers (Claude, ChatGPT, Perplexity).

## [0.1.0] - 2026-04-12

Initial public release.

### Added
- `graphory login`, `graphory status`, `graphory logout`, `graphory --version` CLI commands.
- Local config at `~/.graphory/config.json` with 0600 permissions.
- `Graphory` HTTP client with methods: `search`, `traverse`, `entity`, `timeline`, `stats`, `write`.
- `Graphory.from_config()` helper to instantiate a client from saved credentials.
- `Node` and `Edge` typed models.
- `GraphoryError` exception type for API errors.
- httpx-based transport, Python 3.9+ supported.
