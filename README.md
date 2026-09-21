# mcp-forge — MCP Server

Generate production-ready MCP servers from any source: OpenAPI/Swagger specs, GraphQL APIs, codebases (23 languages incl. COBOL & Fortran), CLI tools and websites. Pass a URL — mcp-forge handles parsing, LLM enrichment, and quality scoring (0–10 on 6 criteria).

This is the public mcp-forge MCP server. Connect it to an AI client such as Claude or Cursor and the agent can generate an MCP server for you from a public URL, with no pre-parsing. For local codebases, CLI tools and any source that is not publicly reachable over HTTPS, use the [mcp-forge CLI](https://github.com/vulcai-io/MCP-FORGE-CLI) instead.

**SSE endpoint**: `https://mcp.mcp-forge.vulcai.io/sse`  
**Auth**: `Authorization: Bearer <YOUR_LICENSE_KEY>`  
**Get a license**: <https://mcp-forge.vulcai.io/register> (free during the public beta)

## Try it

Once the server is connected, ask your agent:

> Generate an MCP server from https://petstore3.swagger.io/api/v3/openapi.json

The result includes a download URL for the generated server, a preview of its first tools, and its quality score (`eval_score`, 0–10) with a per-criterion report.

## Tools

### generate_from_openapi

Generate a complete MCP server from an OpenAPI/Swagger spec URL. Pass the URL of the spec file (e.g. `/openapi.json`, `/swagger.json`) — not the API base URL. Server-side parsing, no pre-processing required.

### generate_from_graphql

Generate a complete MCP server from a GraphQL endpoint. Uses server-side introspection. Works with endpoints that have introspection enabled (dev/staging environments).

### generate_from_website

Generate an MCP server by analyzing a public website's structure and patterns. Works best with server-rendered HTML pages.

### generate_async

Submit a long-running generation job and get a `job_id` immediately (< 100 ms). Use `get_job_status` to poll for completion. Recommended for large specs or slow endpoints.

### get_job_status

Poll the status of an async generation job. Returns `pending`, `running`, `done`, or `failed`. When `done`, includes the full generation result with `bundle_url`, `eval_score`, and `preview_tools`.

### get_health

Check the health status of the mcp-forge API. Returns `status`, `version`, `license_mode`, `region`, and `degraded_features`.

### validate_license

Validate a license key and retrieve plan details: `valid`, `plan`, `tenant_id`, `valid_until`, `license_mode`. Always returns HTTP 200 (never 401).

### get_quota

Retrieve quota usage for the current license: `sources_used`, `sources_limit` (or `"unlimited"`), `is_unlimited`, `valid_until`.

## Configuration

Generic MCP client configuration:

```json
{
  "mcpServers": {
    "mcp-forge": {
      "url": "https://mcp.mcp-forge.vulcai.io/sse",
      "headers": {
        "Authorization": "Bearer <YOUR_LICENSE_KEY>"
      }
    }
  }
}
```

Claude Code:

```bash
claude mcp add --transport sse mcp-forge https://mcp.mcp-forge.vulcai.io/sse \
  --header "Authorization: Bearer <YOUR_LICENSE_KEY>"
```

A license key is required. Keep it out of any repository and use your client's secret handling where available.

## Limitations

- GraphQL generation needs introspection enabled on the endpoint, which is often disabled in production.
- JavaScript-only websites (single-page apps) are not analyzed by this server: use the CLI with its `[web]` extra.
- A generated server usually needs some adjustments for your own configuration. The evaluator report tells you where to look.
- If a generation returns 0 tools, see the [troubleshooting guide](https://mcp-forge.vulcai.io/docs/troubleshooting/no-tools).

## Links

- Homepage: <https://mcp-forge.vulcai.io>
- CLI repository: <https://github.com/vulcai-io/MCP-FORGE-CLI>
- CLI on PyPI: <https://pypi.org/project/vulcai-mcp-forge-cli/>
- Trust & Security: <https://mcp-forge.vulcai.io/trust>
- Troubleshooting: <https://mcp-forge.vulcai.io/docs/troubleshooting/no-tools>
- Changelog: [CHANGELOG.md](CHANGELOG.md)
