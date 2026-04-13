# Changelog

All notable changes to the Graphory Python SDK are documented in this file.

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
