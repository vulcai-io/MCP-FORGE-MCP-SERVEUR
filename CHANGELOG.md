# Changelog

All notable changes to the public mcp-forge MCP server
(`https://mcp.mcp-forge.vulcai.io/sse`) and to the API behaviour that AI agents see
through it are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

Changes to the CLI (`vulcai-mcp-forge-cli` on PyPI) are tracked in the
[MCP-FORGE-CLI repository](https://github.com/vulcai-io/MCP-FORGE-CLI/blob/main/CHANGELOG.md).

---

## [0.2.3] - 2026-07-20

### Added

- Troubleshooting page for the `NO_TOOLS_EXTRACTED` error, covering the four most
  common causes: wrong OpenAPI URL, GraphQL introspection disabled, JavaScript
  single-page app needing the CLI with Playwright, and a codebase with no public
  functions.

### Fixed

- `hint_url` in error responses now points to the live troubleshooting page.

## [0.2.2] - 2026-07-20

### Changed

- Quality evaluation and tool-description enrichment are more resilient to
  rate limits of their underlying model providers, thanks to automatic fallback.

## [0.2.1] - 2026-07-20

### Added

- `get_health` now returns `version`, `region` and `degraded_features` (for example
  `["llm_enrichment"]`). `status` becomes `"degraded"` when that list is not empty.

### Changed

- `validate_license` and `get_quota` are fully separated:
  - `validate_license` returns identity fields only (`valid`, `reason`, `plan`,
    `tenant_id`, `valid_until`, `license_mode`) and always answers HTTP 200,
    never 401. The new `reason` field explains an invalid key: `no key provided`,
    `unknown key`, `license revoked` or `license expired`.
  - Quota fields (`sources_used`, `sources_limit`) are exclusive to `get_quota`.
- Descriptions of `get_health`, `validate_license`, `get_quota` and all
  `generate_from_*` tools now follow a structured "When to call / Prerequisites /
  Returns JSON" format that guides agents.

## [0.2.0] - 2026-05-29

**Breaking release.** CLI `0.1.x` is not compatible with the `0.2.0` backend.

### Added

- High-level tools for agents, which need only a public URL and no pre-parsing:
  - `generate_from_openapi(spec_url, name?)`
  - `generate_from_graphql(endpoint_url, name?, headers?)`
  - `generate_from_website(url, name?)`
- Async job pattern for long generations, with the tools `generate_async` and
  `get_job_status` (a job is created in under 100 ms, and results are polled).
- Authenticated download of the generated server as a zip archive. Tenants are
  isolated: a wrong tenant receives a 404.
- `eval_score` (0-10) and a breakdown over six criteria (structure, coverage, auth,
  docs, fidelity, robustness) in every generation response.
- Public Trust & Security page (encryption, hosting, data retention, LLM access,
  compliance, subprocessors).

### Changed (breaking)

- Generation responses no longer inline file contents. They return `bundle_url`,
  `preview_tools` (the first 5 generated tools), `eval_score` and `eval_report`.
- A generation that extracts zero tools returns `success: false` with structured
  `errors` and an actionable hint, instead of `success: true` with 0 tools.
- Unlimited plans now report `"unlimited"` and `is_unlimited: true` instead of `null`.
- The license model is renamed from `sites` to `sources`: API fields `sites_used` and
  `sites_limit` become `sources_used` and `sources_limit`.
- Most generation parameters (`title`, `description`, `forge_version`, `tools`,
  `auth_schemes`) are optional with sensible defaults.
- `create_generation` is deprecated for agents (kept for CLI compatibility):
  use `generate_from_openapi` or `generate_async` instead.

### Fixed

- Descriptions of the high-level tools were silently empty for agents. They now show
  the full text, including limitations (GraphQL introspection disabled in
  production, JavaScript-only sites needing the CLI) and the pointer to the CLI for
  non-public sources. A regression test keeps them populated.
